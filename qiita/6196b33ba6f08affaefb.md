---
title: CraftLaunch から Giraffe+ へ移行しようとした
tags: giraffe
author: hokorobi
slide: false
---
# CraftLaunch から Giraffe+ へ移行しようとした

今現在 CraftLaunch 2.08 を使っていて、Giraffe+ 0.7.9.1556_20140406 へ移行しようとした話。
CraftLaunch 3.30 でも良かったんじゃないかと思うけど衝動的だったので。

## CraftLaunch 2.08 で気になるところ

1. 日本語を Delete で削除すると 1 バイト分しか削除されない。
2. Google 検索に ue を使わないと文字化けする。
3. 古い

## Giraffe+ をインストールして試したこと

### Google 検索コマンド

doc/manual.html に記載の通り、以下を Data へ格納。文字化けしない。

```:Google.girrafe
(Googleで検索)
(google.ico)
include((www))
Shell.Execute(cat((http://www.google.co.jp/search?ie=sjis&q=)www:url-encode(param(0))))
```

### コマンドの優先順位指定

Priority.txt に優先したいコマンドを記載。
何も指定しないと文字列順（？）になる。
実行することで優先順位が変わることもない。

### コマンド実行後 Giraffe+ が非表示になったら IME を Off にする

ユーザスクリプト用の Script2/settings/setup へ以下を追加して、設定から有効にした。

```:IMEOffInactive.giraffe
LOCALE& 'japanese', 'ディアクティベイト時にIME Off', 'IME Off when deactivating'
AddEvent& 'Deactivate',	[
	Giraffe.SetImeState(false Giraffe.MainWnd.m_hWnd)
]
```

## Giraffe+ で気になるところ

1. モディファイアキーでの動作の変更が簡単にはできない？　スクリプトを書かないといけなさそう。
2. Google 日本語入力で変換候補を表示している際に、ウィンドウの幅からはみ出ると、候補ウィンドウを右か下のどちらに表示するかしっかり決まらないらしくちらつく。Google日本語入力のみかな？
3. 履歴表示時のウィンドウサイズの変更が遅い。AutoResize.SetTextTimeOutで変更可能だったので解決。
4. ウィンドウサイズが変更されるまで左に文字がはみ出すのが気持ち悪い
5. 履歴表示時にウィンドウサイズの変更が頻繁にあると鬱陶しい。AutoResize.Enable を 2 にすることで緩和された。

## そもそも

CraftLaunch でも大したことをしていないので、わざわざ変える必要はないかなと思い直した。
Giraffe+ できになるところの 2. が解決できるのなら移ってもいいとは思う。

