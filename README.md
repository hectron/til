# TIL

Want to see what I've learned so far? So do I!

This repository is tightly coupled with my Vimwiki workflow.

## vimwiki integration

I use vimwiki to write my diary notes into `content/til`.


```vimrc
" set .md as default vim-wiki format
let wiki = {}
let wiki.<some-setting> = "<some-value>"
...
...
let wiki_list = [wiki]

" til wiki configuration
"   make sure the repository is cloned, and the env var is set
if $TIL_DIR != ""
  let til_wiki = {}
  let til_wiki.path = $TIL_DIR . "/content"
  let til_wiki.syntax = "markdown"
  let til_wiki.ext = ".md"

  let til_wiki.diary_rel_path = "til/"

  let til_wiki.auto_diary_index = 1
  let til_wiki.auto_generate_links = 1
  let til_wiki.auto_generate_tags = 1
  let til_wiki.auto_toc = 1 
  call add(wiki_list, til_wiki)
endif

let g:vimwiki_list = wiki_list
```
