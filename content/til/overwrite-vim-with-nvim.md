---
title: Overwrite using vim with nvim
date: 2021-08-18
summary: >
  So you installed `neovim`, but want to use it anywhere `vim` is used. Follow this guide to find out how.
tags:
  - nvim
  - vim
  - cli
---

# tl;dr

- `update-alternatives` if you're using Linux.
- Create symlink named `vim` which points to `nvim` in your `~/.local/bin/`.
- Create an alias for `nvim` titled `vim` in your `.aliases` file.

## `update-alternatives`

If you're using Linux, this is the most straightforward way to do it. Linux has a tool titled `update-alternatives` that will safely create a symlink to replace another command.

I'd recommend you check out the `tldr` page for `update-alternatives`, or do a quick search to find how it works.

To use `nvim` in favor of `vim`:

```bash
sudo update-alternatives $(which vim) vim $(which nvim) 100
```

To use `nvim` in favor of `vi`:

```bash
sudo update-alternatives $(which vi) vi $(which nvim) 100
```

## `~/.local/bin` override

One increasingly popular way to install binaries locally (or override existing ones) is to have a `~/.local/bin` directory. Most operating systems now will support this, but if not -- just add it to your `$PATH`.

The following is how I went about using this approach:

```bash
ln -s $(which nvim) ~/.local/bin/vim
```

## aliasing

You can also just add an alias to your `.aliases` file (or any of your shell configuration files):

```bash
alias vim=nvim
```
