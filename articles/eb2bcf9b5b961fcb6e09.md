---
title: "altercmd -> lexima おためし"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Vim"]
published: true
---

[tyru/vim-altercmd](https://github.com/tyru/vim-altercmd) を [cohama/lexima.vim](https://github.com/cohama/lexima.vim) で代替できるという話を vim-jp Slack で見たので試してみる。

こんな感じで書くのかな。
```vim
if dein#check_install('vim-quickrun') == 0
  call lexima#add_rule(#{
        \   at   : '^\%#',
        \   char : 'quick',
        \   input: 'Quickrun<Space>',
        \   mode : ':',
        \})
endif

if dein#check_install('linediff.vim') == 0
  call lexima#add_rule(#{
        \   at   : '^\%#',
        \   char : 'diffli',
        \   input: 'Linediff',
        \   mode : ':',
        \})
endif
```

mode ':' で設定してもインクリメンタルサーチにも少し影響が出るのね。
char 'quick', input 'Quickrun<Space>' にすると quick が入力されるまで画面には一文字ずつ表示されるだけになりインクリメンタルサーチも開始されない。
quic を検索するには少し待たされる。

altercmd の cap[ture] は lexima でも cap%[ture] と書けるということで便利情報かと思ったけど、capt で展開されるので意味はない？

まだ altercmd は使い続けよう。

