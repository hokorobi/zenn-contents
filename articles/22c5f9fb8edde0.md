---
title: "ctrlp.vimのエクステンションの作り方"
emoji: "💨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

# ctrlp.vimのエクステンションの作り方

https://koturn.hatenablog.com/entry/2015/11/19/200000

## はじめに

この記事は [ctrlpvim/ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim) のエクステンションの作り方についてまとめた記事です。
公式リポジトリの `extensions` ブランチに書かれている内容、および先人の各種記事に個人的な知見を追加して構成しています。

### 参考記事

* Vimプラグインの拡張機能プラグインを作ってVimをさらに使いやすくしよう - 29th Sta.
* ctrlp.vimのExtensionを書くときに:NeoBundleLazy autoloadを考慮してg:ctrlp_builtinsをそのまま使わない - cafegale
* CtrlPエクステンションでハイライト

---

## エクステンションの作り方

作成手順の基本は非常にシンプルです。

1. **`g:ctrlp_ext_vars`** というグローバル変数のリストに、エクステンションの設定用辞書を追加する。
2. 関数 **`ctrlp#init()`** に該当エクステンションのインデックス（ID）を渡して呼び出す。

### ファイル配置のアテンション

作成するエクステンションは、**1つのファイル**にまとめて `autoload/ctrlp/{extension_name}.vim` に配置します。
リスト `g:ctrlp_extensions` に登録されている場合、ctrlp.vim 本体の読み込み時に `runtime autoload/ctrlp/{extension_name}.vim` として読み込まれるためです。

---

## サンプルコード

以下は「カレントディレクトリのファイル一覧を表示し、選択したファイルの行数を `echo` する」というシンプルなサンプルの例です。

### 1. `plugin/sample.vim`

```vim
if exists('g:loaded_sample')
  finish
endif
let g:loaded_sample = 1

let s:save_cpo = &cpo
set cpo&vim

command! CtrlPSample call ctrlp#init(ctrlp#sample#id())

let &cpo = s:save_cpo
unlet s:save_cpo

```

### 2. `autoload/ctrlp/sample.vim`

```vim
if exists('g:loaded_ctrlp_sample') && g:loaded_ctrlp_sample
  finish
endif
let g:loaded_ctrlp_sample = 1

let s:save_cpo = &cpo
set cpo&vim

let s:sample_var = {
\   'init': 'ctrlp#sample#init()',
\   'accept': 'ctrlp#sample#accept',
\   'lname': 'sample',
\   'sname': 'sample',
\   'type': 'path',
\   'nolim': 1
\}

if exists('g:ctrlp_ext_vars') && !empty(g:ctrlp_ext_vars)
  let g:ctrlp_ext_vars = add(g:ctrlp_ext_vars, s:sample_var)
else
  let g:ctrlp_ext_vars = [s:sample_var]
endif

let s:id = g:ctrlp_builtins + len(g:ctrlp_ext_vars)

function! ctrlp#sample#id() abort
  return s:id
endfunction

function! ctrlp#sample#init() abort
  return filter(split(globpath('.', '*', 1), "\n"), 'filereadable(v:val)')
endfunction

function! ctrlp#sample#accept(mode, str) abort
  call ctrlp#exit()
  echo len(readfile(a:str))
endfunction

let &cpo = s:save_cpo
unlet s:save_cpo

```

> **Note**: autoload ファイルであっても ctrlp.vim 本体から直接 `runtime` で読まれる可能性があるため、先頭に二重読み込み防止の記述（`g:loaded_ctrlp_sample`）が必要です。

※このサンプルコードは GitHub リポジトリ `koturn/ctrlp-sample` でも参照・試すことができます。

---

## `g:ctrlp_ext_vars` に設定する辞書のキー一覧

エクステンションの挙動を定義する辞書オブジェクト（`s:sample_var` など）のキー解説です。

### 必須のキー

| キー | 型 | 説明 |
| --- | --- | --- |
| **`init`** | String | 評価されて候補リストを返す関数呼び出し文字列（例: `'ctrlp#sample#init()'`)。<br>

<br>CtrlPのバッファに移動した直後に評価・実行される。 |
| **`accept`** | String | 候補が選択されたときに呼び出される**関数名**（末尾の括弧 `()` は不要）。<br>

<br>シグネチャ例: `ctrlp#sample#accept(mode, str)` |
| **`lname`** | String | ステータスライン等に表示される省略無しの名前（Long Name）。 |
| **`sname`** | String | ステータスライン等に表示される省略名（Short Name）。 |
| **`type`** | String | マッチング処理の種別。以下から選択：<br>

<br>・`'line'`: 各候補全体に対して入力とマッチ<br>

<br>・`'path'`: パス文字列としてマッチ（一致部分がハイライトされる）<br>

<br>・`'tabs'`: 最初のタブ文字までをマッチ対象とする<br>

<br>・`'tabe'`: 最後のタブ文字までをマッチ対象とする |

#### `accept` 関数の引数仕様

* **`mode`**: 選択確定時に押されたキー種別
* `<CR>` 押下時 $\rightarrow$ `'e'`
* `<C-v>` 押下時 $\rightarrow$ `'v'`
* `<C-t>` 押下時 $\rightarrow$ `'t'`
* `<C-x>` 押下時 $\rightarrow$ `'h'`


* **`str`**: 選択された候補の文字列
* **処理ルール**: 関数の先頭付近で `call ctrlp#exit()` を呼んでバッファを閉じます。

---

### 任意のキー

| キー | 型 | 説明 |
| --- | --- | --- |
| **`enter`** | String | CtrlPが起動する**直前**に評価される関数呼び出し文字列。元バッファの `filetype` 取得などに使用。 |
| **`exit`** | String | CtrlPが終了する**直前**に評価される関数呼び出し文字列。 |
| **`opts`** | String | オプション設定などで利用される関数呼び出し文字列。`enter` より先に呼び出される。 |
| **`sort`** | Number | 候補のソート指定。`0`: ソートしない（デフォルト）、`1`: ソートする。 |
| **`specinput`** | Number | 特殊入力の有効化。`0`: 不可（デフォルト）、`1`: 可能。<br>

<br>`..`（階層を上る）や `@cd`（カレントディレクトリ変更）といった入力を可能にする。 |

---

### 非公式・未ドキュメントのキー（任意）

| キー | 型 | 説明 |
| --- | --- | --- |
| **`nolim`** | Number | 件数制限の解除。`0`: 制限あり（デフォルト）、`1`: 無制限（スクロール可能）。検索しづらい日本語候補などに便利。 |
| **`opmul`** | Number | 複数選択（Open Multiple）の許可。`0`: 不可（デフォルト）、`1`: 許可。<br>

<br>※複数選択時のアクションは**原則としてファイルを開く動作のみ**に制限される点に注意。 |
| **`wipe`** | String | `<F7>` キーでマークされた候補を削除・除外する際のアクション関数名。<br>

<br>シグネチャ例: `ctrlp#sample#wipe(entries)` |

#### `wipe` の実装例

`opmul` と `wipe` を組み合わせて削除処理（フィルタリング）を行うコードの実装パターンです。

```vim
function! ctrlp#sample#init() abort
  " 候補をスクリプトローカルに保持（終了時などにunlet推奨）
  let s:candidates = filter(split(globpath('.', '*', 1), "\n"), 'filereadable(v:val)')
  return s:candidates
endfunction

function! ctrlp#sample#wipe(entries) abort
  return empty(a:entries) ? [] : filter(s:candidates, 'index(a:entries, v:val) == -1')
endfunction

```

---

## 補足：CtrlP起動時にワーキングディレクトリを変更する

`ctrlp#init()` は第2引数に辞書を取ることもでき、`dir` キーを指定することで起動時のワーキングディレクトリを変更できます。

```vim
call ctrlp#init(ctrlp#sample#id(), { 'dir': '/path/to/dir' })

```

相対パスの追跡や特定のディレクトリ基準で検索をスタートさせたい場合に有効です。
