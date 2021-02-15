---
title: Open new tmux panes in current dir
date: 2021-02-15
summary: Learn this little simple lifehack to improve your quality of life
---

# tl;dr

```
# Set new panes to open in current directory
bind c new-window -c "#{pane_current_path}"
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
```

# The story

Do you ever have the problem where you open up tmux in a specific directory, and
every window + pane is then opened at that specific directory? Fret no more!

You can use `pane_current_path` to let tmux know which directory your pane
currently is, and open the new window in that directory. It's a super easy
addition to your `.tmux.conf` file:

```
# Set new panes to open in current directory
bind c new-window -c "#{pane_current_path}"
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"
```
