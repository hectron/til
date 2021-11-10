---
title: gopls asdf
date: 2021-10-10
summary: Get `gopls` working
tags:
  - vim
  - go
  - asdf
---

# tl;dr

Ensure that your `GOPATH` has your `asdf` go env!

```zsh
export GOROOT="$HOME/.asdf/installs/golang/1.17.go"
export GOPATH=$(go env GOPATH)
export PATH="$PATH:$(go env GOPATH)/bin"
```

# Preface

I apologise if this is all over the place. I mainly spent the last two hours searching for a solution, and was able to
conjure up a solution (detailed below). I'm primarily writing this for myself; please excuse any bouncing between
ideas/concepts.

# Background

I've been playing around with `nvim` for a bit of time now. I haven't taken advantage of the native `lsp` protocol built
into version 0.5+. I've been trying to write more go for personal projects, such as
[`fauci.d`](https://github.com/hectron/fauci.d/), so my desire for LSP quickly spiked. For Go, the `LSP` server is
`gopls`.

# Getting nvim configured with gopls

This step is pretty easy. In your nvim config files, you configure the `lsp` plugin of your choice to use `gopls`. I'm
using `neovim/nvim-lspconfig` plugin, so for me it looks like:

```vim
lua << EOF
local nvim_lsp = require('lspconfig')

local on_attach = function(client, bufnr)
  -- configuring the lspconfig plugin
end

-- this tells nvim lsp to register a new language server, named gopls, and use the configuration from above.
nvim_lsp.gopls.setup {
  on_attach = on_attach,
}
EOF
```

Once you save and reload your nvim, you should be good to go -- but I wasn't. I ran into the following issue when navigating into
go files:

```
cmd ["gopls"] is not executable
```

This means that nvim cannot find an executable named `gopls`. You can check by running the following command in nvim:
`:echo executable('gopls')`. If it returns a 1, then it is available and ready to be used. **I kept getting 0**, despite
installing the binary manually.

TLDR, this turned out to be a problem because I never set my `$GOPATH` and `$GOROOT` environment variables. `asdf`
allows you to install multiple versions of `go`, so I'm still a little unclear how to manage these. However, the
following configuration in my `.zshrc` did the trick:

```zsh
export GOROOT="$HOME/.asdf/installs/golang/1.17.go"
export GOPATH=$(go env GOPATH)
export PATH="$PATH:$(go env GOPATH)/bin"
```

Once this was set, I fired up `nvim` and loaded up a go file, only to have everything work.
