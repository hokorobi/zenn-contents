---
title: dein.vim 用に無いよりはマシな snippet を書いた
tags: Vim
author: hokorobi
slide: false
---

```
snippet tap
options head, indent
	" }}}3  ${1:plugin} {{{3
	
	if dein#tap('$1')
	
	  function! s:$1${2}_on_source() abort
	    ${4}
	  endfunction

	  autocmd vimrc User dein#source#$1 call s:$1${3}_on_source()

	endif
```

