---
title: My Tmux Memo
date: 2025-09-07 23:52:28
excerpt: Quick tmux memo and mysterious product teaser :).
index_img: /img/cover/mytmux.jpg
categories:
  - Efficient Tools
tags:
  - Tmux
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# My Tmux

## Tmux Memo

- create a session

```bash
tmux new -s project-1
```

- enter a session

```bash
tmux attach -t project-1
```

- create a new window inside a session: `Ctrl b` + `c`

- switching between windows

  - `Ctrl b` + `<number>` (The number of the window)
  - `Ctrl b` + `n`: switch to the next window
  - `Ctrl b` + `p`: switch to the previous window

- show all the windows: `Ctrl b` + `w`


## TmuxMaster?

It is on the way...
