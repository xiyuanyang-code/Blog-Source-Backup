---
title: Git Advanced Memo
date: 2025-08-02 01:35:15
index_img: /img/cover/gittutorial.jpg
excerpt: The advanced introduction to Git, including a memo for most frequently used git command, and the explanation of Version Control Philosophy and Joint Development.
categories:
  - Efficient Tools
tags:
  - Git
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Git Advanced Memo

## How does Git work?

{% note primary %}

Know Git is useful is important, but knowing why Git is useful is more important!

If you are a green hand for git, just ignore this section and find other git tutorial!

{% endnote %}

## Quick Memo for Git command

- `git add {file}`: Add files from **Working Directory** to **Staging**.

- `git commit -m {message}`: Commit file from **Staging** to **Local Repo**.

- `git reset`: Remove files from **Staging** to **Working Directory**.

- `git pull`: Pulling files from **Remote Repo** to **Local Repo**.

- `git push`: Pushing files from **Local Repo** to **Remote Repo**.

- `git stash`: Stash from **Working Directory** to **Stash Locations**.

    - `git stash pop`: Get stash from **stash locations** to **working directory**.

- `git fetch`

- `git merge`
