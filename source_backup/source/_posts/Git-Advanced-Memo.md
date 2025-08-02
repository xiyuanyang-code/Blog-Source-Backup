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

This section will be done in the near future.

Relevant Website: [Missing Semester for Git](https://missing.csail.mit.edu/2020/version-control/)

## Quick Memo for Git command

Recommend this blog[^1] for knowing more Git command.

- File movement/transfer between the Working Directory and the Staging Area.

  - `git add <file_name>`: Add files from **Working Directory** to **Staging**.

  - `git reset HEAD <file_name>` or `git restore --staged <file_name>`: Remove files from **Staging** to **Working Directory**.

- File movement between the Staging Area and the Local Repo.

  - `git commit -m <message>`: Commit file from **Staging** to **Local Repo**.

  - `git reset --soft HEAD~1`: Remove the latest commit from **Local Repo** to **Staging Area**.

    - `git reset --mixed HEAD~1`: Remove directly to the current working directory.

- File movement between **remote branch tracking** and **remote branch**:

  - `git fetch`: Fetching new updated to Remote branch tracking.

- `git pull`: Pulling files from **Remote Repo** to **Current Working Directory**.

  - First, `git fetch`.

  - Then, `git merge`, for example, merge branch `origin/main` into `main`.

- `git push`: Pushing files from **Local Repo** to **Remote Repo**.

- `git stash`: Stash from **Working Directory** to **Stash Locations**.

    - `git stash pop`: Get stash from **stash locations** to **working directory**.

    - `git stash list`: List all the stash.

- `git merge` & `git rebase`

  - Git Merge: Integrate all the commits of one branch into another.

    - `git merge <branch>`: merge branch into current branch. (Fast Forward Merge & 3-Way Merge)

    - `git rebase <branch>`: rewrite history for branch, to rewrite history.

      - Never Use `git rebase` in `main` branch or other public branches!

{% note primary %}

| Command | Moves HEAD | Modifies Staging Area | Modifies Working Directory | Common Use Case |
|:--- |:--- |:--- |:--- |:--- |
| `git reset --soft` | Yes | No | No | Undoes commits but keeps all changes in the staging area, ready to be re-committed. |
| `git reset --mixed` | Yes | Yes | No | Undoes commits and unstages changes, moving them to the working directory. |
| `git reset --hard` | Yes | Yes | Yes | Completely discards commits and all local changes, reverting to a previous version. |

{% endnote %}

{% note info %}

![Git Command](https://s1.imagehub.cc/images/2025/08/02/d782b9153abb311986a9d4cd2b8d52d8.jpg)

{% endnote %}

## References

[^1]: [图解 Git 命令](https://marklodato.github.io/visual-git-guide/index-zh-cn.html)