---
title: Git config's includeIf for multiple configs
date: 2021-03-22
summary: >
  Git introduced a directive to allow conditional git configurations.
  Let's explore using it for multiple configs.
tags:
  - misc
  - git
---

# tl;dr

```git
[includeIf "gitdir:~/my/personal/directory"]
  path = ~/.gitconfig.local
  
[includeIf "gitdir:~/my/work/directory"]
  path = ~/.gitconfig.work
  
# In ~/.gitconfig.local
[author]
  email = "personal-email@provider.com"
  name = "first last"
  
# In ~/.gitconfig.work
[author]
  email = "address@work.com
  name = "work-username"
```
