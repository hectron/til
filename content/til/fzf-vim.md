---
title: fzf-vim as your search guide
date: 2021-02-16
summary: A better way to search for files in vim
---

# tl;dr

Use [`fzf.vim`](https://github.com/junegunn/fzf.vim) to find files quickly (more quickly than say, [`ctrlp`](https://github.com/kien/ctrlp.vim)).

```vim
" fzf#install() makes sure that you have the latest binary, but it's optional, so you can omit it if you use a plugin manager that doesn't support hooks.
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'
```

# Motivation

I've been using [`ctrlp`](https://github.com/kien/ctrlp.vim) since I started
using Vim. I recently joined [Braintree, which heavily uses
Vim](https://github.com/braintreeps/vim_dotfiles), and noticed that our
`ctrl+p` behavior loaded files much quicker and with minimal caching.
Naturally, I tried to find out what was using Ctrl+P in Vim.

I loaded up the following command in Vim's command-mode:

```vim
:Maps

" In the following menu, I searched for
" <C-P> 
" And found:
" :Files<CR>
```

So there was something that was declaring a `:Files` command. Next, I found out where that command was being defined. Again, I ran the following in Vim's command-mode:

```vim
:verbose command Files

" This returned the following:

"     Name        Args       Address   Complete  Definition
" !   Files       ?                    dir           call fzf#vim#files(<q-args>, <bang>0)
"         Last set from ~/.vim/plugged/fzf.vim/plugin/fzf.vim line 37
```

Therefore, I was able to notice that the
[`fzf.vim`](https://github.com/junegunn/fzf.vim) plug-in was responsible for
this functionality. Once I found the
[`fzf.vim`](https://github.com/junegunn/fzf.vim) plugin, I saw that it's [one of the supported commands](https://github.com/junegunn/fzf.vim#commands)

# Benefits

Asynchronous loading of files means that you can more rapidly search, and don't have to periodically refresh the cache/indexing (unlike `CtrlP`).
