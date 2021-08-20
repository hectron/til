---
title: Fully moving onto neovim
date: 2021-08-19
summary: >
  This is a brief post on how I fully moved from `vim` to `nvim`.
tags:
 - cli
 - nvim
 - vim
---

# tl;dr

Setup `~/.config/nvim/init.vim`. If you want modularity, use the following as well:

```
~/.config/nvim/plugs.vim                     # this is where vim-plugs are specified
~/.config/nvim/mappings.vim                  # this is where custom key-mappings are set
~/.config/nvim/after/plugin/<plugin>.rc.vim  # this is where any customization can be done on a per-plugin basis
```

A `~/.config/nvim/after/plugin/<plugin>.rc.vim` looks like:

```vim
" Check to see if the plugin was loaded at all
if !exists("g:loaded_<plugin>")
  " Stop sourcing this file if the plugin was not found
  " @see :help exists
  finish
endif

" any customization happens below here
```

## The setup

Neovim's primary user configuration directory is:

```
Unix                              ~/.config/nvim/
Windows                           ~/AppData/Local/nvim/
If $XDG_CONFIG_HOME is defined:   $XDG_CONFIG_HOME/nvim

For information, see `:help init.vim`
```

