---
title: "CtrlP を使って git log からコミットを選択して、そのコミット時点のカレントバッファのファイルを表示する"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Vim"]
published: true
---

CtrlP と ctrlp-generic と gin.vim を使います。

https://github.com/ctrlpvim/ctrlp.vim

https://github.com/christoomey/ctrlp-generic

https://github.com/lambdalisue/gin.vim

それぞれのプラグインを読み込んで、以下を vimrc に書きます。
過去の内容を表示したいファイルを開いた状態で `<Space>gbo` を入力すると、で `git log` の結果が CtrlP で表示され、選択したコミット時点のファイルが vsplit で表示されます。

```vim
function! GinEditCallback(selected_value) abort
  " call feedkeys(printf(";GinEdit ++opener=vsplit %s %%", matchstr(a:selected_value, '\w\+')))
  call matchstr(a:selected_value, '\w\+')
  \  ->printf(":GinEdit ++opener=vsplit %s %%")
  \  ->execute()
endfunction
nnoremap <Space>gbo <Cmd>call CtrlPGeneric(systemlist('git log --date=short --pretty=format:"%h %cd %s"'), 'GinEditCallback')<CR>
```

