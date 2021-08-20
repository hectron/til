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
# this is where vim-plugs are specified
~/.config/nvim/plugs.vim

# this is where custom key-mappings are set
~/.config/nvim/mappings.vim

# this is where any customization can be done on a per-plugin basis
~/.config/nvim/after/plugin/<plugin>.rc.vim
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
# For information, see `:help init.vim`

# In Unix
~/.config/nvim/

# In Windows
~/AppData/Local/nvim/

# If $XDG_CONFIG_HOME is defined
$XDG_CONFIG_HOME/nvim
```

Much like Vim, I use `vim-plug` as my plugin manager for Neovim.  [My neovim directory structure looks like](https://github.com/hectron/dotfiles/tree/235d0757964bbdf2ce8b7495d38f330368d869db/nvim/.config/nvim):

```
nvim/
  - init.vim
  - plug.vim
  - maps.vim
  - <language>-mappings.vim
  - after/
    - plugin/
      - <plugin>.rc.vim
      - <plugin2>.rc.lua
      ...
      - <pluginN>.rc.vim
```

## init file

The `init` is the `.vimrc` file equivalent, and can be either vimscript or lua (`init.vim` or `init.lua`). I went with vimscript, because my pre-existing `.vimrc` file is in `vimscript`, so I figured it'd be easier to adapt.

In this file, I have a conditional to auto-install `vim-plug` at the very top:

```vim
syntax on

" Install vim-plug automatically
let data_dir = stdpath("data") . "/site"

if empty(glob(data_dir . "/autoload/plug.vim"))
  silent "!curl -fLo ".data_dir."/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim"
  autocmd VimEnter * PlugInstall --sync | source $MYVIMRC
endif
```

Besides automatically installing `vim-plug`, I have some defaults that I use (`hlsearch`, `number`, `wrap` to name a few). Below the defaults, I use `runtime` to reference the top-level files (`plug.vim`, `maps.vim`):

```vim
runtime plug.vim " install plugins first
runtime maps.vim " install any custom mappings not necessarily tied to a plugin
```

As neovim is parsing this file, the plugins are defined/installed first, then the custom mappings are set and lastly the `after/plugin` directory is sourced. For more information on this, check out `:help ftplugin`

## plug.vim

This file is where I store my `vim-plug` plugin definitions. There's nothing really special about this file. It looks like:

```vim
let g:plug_home = stdpath("data") . "/plugged"

call plug#begin()
" Default plugins
Plug 'scrooloose/nerdtree'

" Language plugins
Plug 'fatih/vim-go', { 'do': ':GoUpdateBinaries' }

" Colors
Plug 'pineapplegiant/spaceduck', { 'branch': 'main' }

" Neovim 0.5+ plugins, usually LSP related plugins
" This is interpreted as nvim >= 0.5
if has('nvim-0.5')
  Plug 'neovim/nvim-lspconfig'
endif

" Lastly, any specific local plugins that people want installed
if filereadable(expand("~/.vimrc.plugins.local"))
  source ~/.vimrc.plugins.local
endif

call plug#end()
```

## maps.vim

This file contains any plugin-free mappings. They also contain references to language-specific mappings.

```vim
" Escape insert mode using jj
inoremap jj <Esc>

" normal mode mappings
nnoremap <Leader>ev :e $MYVIMRC<CR>
nnoremap <Leader>sv :source $MYVIMRC<CR>

" If the file type is a Ruby file, source the `ruby_mappings.vim` file in the top-level `nvim/' directory.
autocmd FileType ruby runtime ruby_mappings.vim

```

For the sake of example, this is what the `ruby-mapping.vim` file looks like:

```vim
"  Insert a pry
map <LocalLeader>rd Orequire "pry"; binding.pry<ESC>

" If `vimux` has been plugged, use these mappings
if has_key(plug, 'vimux') 
  map <silent> <LocalLeader>rb :wa<CR>:TestFile<CR>
endif
```

## <plugin>.rc.vim or <plugin>.rc.lua

These files live in `nvim/after/plugin/`, which mean that they are sourced after the plugins are loaded.

A plugin file for `NerdTree` (`nvim/after/plugin/nerdtree.rc.vim`) in vimscript looks like:

```vim
" If the plugin is not loaded, stop sourcing this file
" for more info on how this works, check out :help ftplugin
if !exists("g:loaded_nerd_tree")
  finish
endif

" The plugin exists, so define these mappings
map <silent> <LocalLeader>nt :NERDTreeToggle<CR>
map <silent> <LocalLeader>nr :NERDTree<CR>
map <silent> <LocalLeader>nf :NERDTreeFind<CR>

" if you needed to use lua, use a HEREDOC-like syntax

lua << EOF
local var = 1
EOF
```

Note: the file name doesn't really matter, but I like keeping them named after the plugins that are involved.

## Closing thoughts

In my opinion, this is a super natural, clean and ergonomic setup for me. This works, has a clearly defined pattern and allows for the use of lua or vimscript.

The move over was super easy once I established the pattern was established. I might do a post separately for how I setup `treesitter`, `lsp` and `telescope` which really take advantage of neovim.
