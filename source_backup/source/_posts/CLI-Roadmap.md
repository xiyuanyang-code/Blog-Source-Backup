---
title: CLI Roadmap
date: 2025-07-27 15:30:16
index_img: /img/cover/cli.png
excerpt: Several website recommendations for command line interface usage, including fruitful technical blog and courses, and several specific guidelines for tool usage like vim and tmux, etc. This blog also introduce Unix Philosophy for further comprehension.
categories:
  - Efficient Tools
tags:
  - tutorial
  - CLI
---

# CLI Roadmap

> 陈年老博客了，修修补补还是觉的讲的欠点火候，暂且先拿一个半成品出来博君一笑 :)

# CLI-Web Recommendation

## Introduction

As a student in any computer-related field, mastering the command line is an essential skill. Embrace the power and efficiency it offers!


## Blog and Courses

Learning from technical blogs and online courses is a fast track to mastering new technologies, whether you're a beginner or an experienced professional. Complement your learning with relevant documentation.

| Website | Introduction |
|---|---|
| [https://xflops.sjtu.edu.cn/hpc-start-guide/](https://xflops.sjtu.edu.cn/hpc-start-guide/) | Shanghai Jiao Tong University Xflops HPC Team Learning Document (**Highly Recommended**) |
| [https://csdiy.wiki/](https://csdiy.wiki/) | CS Self-Study Guide (**Highly Recommended**) |
| [https://www.geeksforgeeks.org/](https://www.geeksforgeeks.org/) | GeeksforGeeks, featuring a wealth of high-quality technical bloggers (**Highly Recommended**) |
| [https://www.freecodecamp.org/news/](https://www.freecodecamp.org/news/) | FreeCodeCamp, an excellent resource aggregating numerous high-quality computer science tutorials (**Highly Recommended**) |
| [https://missing.csail.mit.edu/](https://missing.csail.mit.edu/) | Missing Semester, a classic course. While not extensive, it can be challenging for absolute beginners, but a second review significantly deepened the author's understanding of the CLI (**Highly Recommended**) |
| [https://www.ruanyifeng.com/blog/archives.html](https://www.ruanyifeng.com/blog/archives.html) | Ruan Yi-feng's Personal Blog |
| [https://www.codecademy.com/learn/learn-the-command-line](https://www.codecademy.com/learn/learn-the-command-line) | Codecademy, a classic interactive learning platform, ideal for beginners with no prior experience |


## Advanced CLI Learning

Elevate your command line proficiency by exploring advanced tools and commands designed to boost productivity.


### Commands

Mastering common and advanced commands is fundamental to efficient command line usage.

| Website | Introduction |
|---|---|
| [https://wangchujiang.com/linux-command/](https://wangchujiang.com/linux-command/) | Linux Command Query Guide |
| [https://tldr.sh/](https://tldr.sh/) | **TLDR**: Simplified and community-driven man pages. |


### Advanced Tools

Boost your workflow by integrating powerful command-line tools and software into your daily routine.

| Website | Introduction |
|---|---|
| [https://yazi-rs.github.io/](https://yazi-rs.github.io/) | **Yazi**: A fast and concise command-line file manager. |
| [https://github.com/ScoopInstaller/Scoop](https://github.com/ScoopInstaller/Scoop) | A package installer for Windows. |
| [https://www.haoyep.com/posts/zsh-config-oh-my-zsh/](https://www.haoyep.com/posts/zsh-config-oh-my-zsh/) | Install a third-party Shell: zsh (**Highly Recommended**. Zsh settings are often more comfortable than bash, and customizing your shell can significantly improve workflow efficiency). |
| [https://www.ruanyifeng.com/blog/2019/10/tmux.html](https://www.ruanyifeng.com/blog/2019/10/tmux.html) | Tmux: A terminal multiplexer. |
| [https://yeasy.gitbook.io/docker_practice](https://yeasy.gitbook.io/docker_practice) | Docker: Containerization technology. |

> There are countless posts and high-quality tutorials on command-line development tools, which are too numerous to list here.


### Vim

Vim is a powerful, modal text editor that stands out in the terminal environment. When mastered, it can surpass the efficiency of other editors like VS Code.

| Website | Introduction |
|---|---|
| [Vim in Missing Semester](https://missing.csail.mit.edu/2020/editors/) | Vim in Missing Semester |
| [https://vim.hizdm.cn/completion/coc-nvim.html](https://vim.hizdm.cn/completion/coc-nvim.html) | Vim Plugin Website |
| [https://github.com/neoclide/coc.nvim](https://github.com/neoclide/coc.nvim) | Make your Vim/Neovim as smart as VS Code, providing intelligent code completion. |
| [https://github.com/junegunn/vim-plug](https://github.com/junegunn/vim-plug) | Vim Plugin Manager |
| [Vim CheatSheet](https://vim.rtorr.com/) | Vim CheatSheet |


### Git

Git is a fast, scalable, distributed revision control system with an unusually rich command set that provides both high-level operations and full access to internals.

| Website | Introduction |
|---|---|
| [https://missing.csail.mit.edu/2020/version-control/](https://missing.csail.mit.edu/2020/version-control/) | Version Control in Missing Semester |
| [https://lau.yeeyu.org/series/git-tutorial](https://lau.yeeyu.org/series/git-tutorial) | Git tutorial by Yiyu Liu |
| [https://ohshitgit.com/](https://ohshitgit.com/) | When you find yourself in a mess with Git, this resource can help. |
| [https://www.conventionalcommits.org/en/v1.0.0/#summary](https://www.conventionalcommits.org/en/v1.0.0/#summary) | Template for git commit message |
| [https://acm.sjtu.edu.cn/wiki/Git](https://acm.sjtu.edu.cn/wiki/Git) | Git wiki written by SJTU-ACM |
| [https://git-scm.com/docs/git](https://git-scm.com/docs/git) | The official Git website |


## Books?

For those who prefer in-depth learning, these books offer comprehensive insights into the command line.

| Website | Introduction |
|---|---|
| [https://www.kea.nu/files/textbooks/humblesec/thelinuxcommandline.pdf](https://www.kea.nu/files/textbooks/humblesec/thelinuxcommandline.pdf) | A lengthy but classic book series. The author attempted to read it but did not finish. |

> Contributions are welcome!

## Make Some Fun

Explore these entertaining command-line projects that demonstrate the playful side of terminal interactions.

- [https://web.mit.edu/mprat/Public/web/Terminus/Web/main.html](https://web.mit.edu/mprat/Public/web/Terminus/Web/main.html)
- [https://github.com/svenstaro/genact](https://github.com/svenstaro/genact)
- [https://github.com/nvbn/thefuck](https://github.com/nvbn/thefuck)

# Why CLI? CLI Philosophy

Why we need CLI? Thinking from the technical point, the fundamental principle is to **enhance working efficiency**, which is the first principle for every tool.

However, it is far more than a tool, it can be a discipline or such kind of philosophy.

## Unix Philosophy

See more on [The art of Unix Programming](http://www.catb.org/~esr/writings/taoup/) for detailed description, or [Wikipedia](https://en.wikipedia.org/wiki/Unix_philosophy)

{% note primary %}

Small is beautiful.
Make each program do one thing well.
Build a prototype as soon as possible.
Choose portability over efficiency.
Store data in flat text files.
Use software leverage to your advantage.
Use shell scripts to increase leverage and portability.
Avoid captive user interfaces.
Make every program a filter.

{% endnote %}

