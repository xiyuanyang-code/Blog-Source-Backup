---
title: My-Bloging-Workflow
date: 2025-07-11 10:45:48
excerpt: Basic introduction to CI&CD in software engineering, including demonstration of my own blog post automation.
tags: 
  - CI & CD
  - Workflow
index_img: /img/cover/cicd.jpg
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# My Bloging Workflow

笔者因为Windows误操作导致环境变量丢失之后，一怒之下把所有的博客写作和相关小插件的开发环境全部转换到了WSL上（真的是光速转换，WSL的配置非常的简洁干净）。

```text
❯ tree -a | grep "\.sh"
│   │   ├── auto_push.sh
│   │   └── run.sh
│   │   ├── autopush.sh
│   │   └── run.sh
│   │   ├── backup.sh
│   │   ├── count.sh
│   │   └── fetching.sh
│   │   │   │   ├── bash_completion.sh
```

上面展示了我写的所有的bash脚本，通过bash脚本可以自动化的**集成命令**并且实现一键运行，提高工作效率。

## CI&CD

Let's see the definition first!

In software engineering, CI/CD is the combined practices of **continuous integration (CI) and continuous delivery (CD)** or, less often, continuous deployment.They are sometimes referred to collectively as continuous development or continuous software development.

### Continuous Integration

#### Git Branches

Git协作可以帮助团队进行版本管理和项目进度控制，和简单的个人微开发不同，团队协作开发的代码往往设计多个人的成千上万行代码，并且设计**开发，测试，部署**等等多个环节，因此，**开发者的核心逻辑是**：**写自己的代码** -> **测试部署** -> **合并到某个分支**。分支不止一个，并且存在分级，因此往往会涉及很多轮的测试部署之后这份代码才会进入main branch。

{% note primary %}

- 主分支 (Main/Master Branch):

    - 这是项目的核心分支，代表着生产环境中的稳定代码。

    - 所有发布到生产环境的代码都应该来自这个分支。

- 开发分支 (Develop Branch) - (Git Flow 策略中常见):

    - 如果团队采用 Git Flow 这种相对复杂的策略，develop 分支会作为所有新功能的集成点。

    - 所有特性分支最终都会合并到 develop 分支。

    - develop 分支通常会**周期性地合并**到 main 分支进行发布。

- 特性分支 (Feature Branches):

    - 为开发每一个新功能或独立任务而创建的短期分支。

    - 通常从 main 或 develop 分支创建。

    - 命名通常与功能相关，如 feature/user-login, feature/payment-gateway。

    - 当特性开发完成并经过测试后，会合并回 main 或 develop 分支。

- 发布分支 (Release Branches) - (Git Flow 策略中常见):

    - 在发布新版本之前，从 develop 分支创建的预发布分支。

    - 主要用于冻结功能，进行最后的 Bug 修复、版本号更新和发布前的测试。

    - 一旦稳定并发布，它会被**合并回 main 和 develop 分支**（以确保 Bug 修复被带入开发流程）。

- 热修复分支 (Hotfix Branches):

- 为了紧急修复生产环境中的 Bug 而创建的短期分支。

- 直接从 main 分支创建，因为需要快速响应生产问题。

- 修复完成后，会合并回 main 分支，并且通常也会合并回 develop 分支，以确保 Bug 修复不被后续开发覆盖

{% endnote %}

#### Integration Hell

集成地狱 (Integration Hell)，特指在项目后期，当团队尝试将来自不同开发者的、**长时间独立开发的代码模块合并到一起时**，所遇到的极其复杂、耗时且令人沮丧的问题集合。

这些问题包括但不限于：

- 各个成员开发的代码模块接口、依赖项等产生冲突

- 分支合并时出现严重的**冲突**问题

- 测试复杂性高，因为涉及到多份代码的测试，难以准确定位是哪里的Bug

> 例如，分别复杂后端开发和用户会话管理的两位后端开发在自己的代码中同时修改了**不兼容**实体类文件（例如User类），这样就导致最终合并的使用产生严重的冲突问题，而这两位开发者为了兼容性不得不再修改自己的代码，进而导致无限循环的错误产生。

#### What is continuous?

集成是**团队开发**过不去的一道坎，尤其面对复杂的代码结构和逻辑时，一点点的对接错误就会产生雪崩式的崩溃。因此，**持续性集成**的概念就产生了，他需要解决的核心问题就是**集成地狱问题**。

CI的核心解决方案是**频繁的集成小组件**，更小的集成意味着更少的Bug和更加频繁的信息同步。

- 频繁合并代码： 开发人员每天多次将代码更改集成到共享主干（通常是主分支）中。

- 自动化构建与测试： 每次代码合并都会触发一个自动化的构建过程，包括**编译代码、运行单元测试、集成测试**等。

- 快速反馈： 如果构建或测试失败，团队会立即收到反馈，从而能够及时发现并修复问题。

CI 的目标是减少集成问题，确保代码库始终处于可发布状态。 通过频繁集成和自动化测试，开发团队可以尽早发现冲突和错误，避免在开发后期才发现难以解决的大问题。

> 例如，在实际情况中，开发者创建了一个新的feature branch（**注意CI的精髓！这一个特性分支只能负责一个小的功能开发**），在完成这部分代码后，会尝试集成到Dev Branch中来测试新功能，如果新功能测试成功就成功合并，如果测试失败就会立即得到反馈，并且回退到原来的状态进行Bug Fixing。

{% note primary %}

一般来说，CI的过程都是**自动化执行的**，意味着**CI 触发**： 此时，CI 服务器通常会检测到这个新的分支或新的提交，并可能在该特性分支上运行预检查（如静态代码分析、单元测试），确保代码质量。 这有助于在合并前就发现问题。

{% endnote %}

### Continuous Delivery

持续交付 (CD) 是 CI 的延伸。它意味着：

- 代码始终处于可发布状态： 成功通过 CI 阶段的代码可以随时部署到生产环境。

- 自动化发布到预生产环境： 代码会自动部署到测试、UAT（用户验收测试）或预生产环境，以便进行进一步的自动化和手动测试。

- 手动部署到生产环境： 部署到生产环境通常需要人工触发，但这仍然是一个高度自动化的过程。

- 持续交付的目标是确保软件**能够随时、轻松地发布** （而不是把Bug全部积累到发布的最后时刻才合并冲突）。 它让发布新功能或修复bug变得更加简单和可预测，但也需要人工决策来批准最终的生产部署。

## My CI&CD for blog

这并不算是一个严格的CI/CD（乐），只是引入了一些自动化的组件。

我的文件结构如下：
```bash
drwxr-xr-x  7 xiyuanyang xiyuanyang 4.0K Jul 11 10:36 Blog-Update-Fetching-Script # 组件：自动化更新博客状态
drwxr-xr-x  5 xiyuanyang xiyuanyang 4.0K Jul 11 10:19 Blog-word-counting          # 组件：博客字数统计  
drwxr-xr-x 10 xiyuanyang xiyuanyang 4.0K Jul 11 11:16 Blog_Main                   # Hexo 主仓库
drwxr-xr-x  5 xiyuanyang xiyuanyang 4.0K Jul 11 10:08 Blog_source                 # 组件：博客源代码备份
```

在完成markdown博客书写之后，运行`my_script/backup.sh`,就会自动实现Hexo博客部署和将更新部分添加到备份文件夹中并且push到远程。

```bash
#!/bin/bash

# bash scripts for auto-backup
# !remember this file must be run in the directory of "Blog_Main"

# using hexo
hexo g -d

# this should be very fast :)
cp -r ./source ../Blog_source/source_backup 
cp -r public/img ../Blog_source/img_backup

# updating git status
cd ../Blog_source || exit
git add .
git commit -m "Upd: $(date '+%Y-%m-%d %H:%M:%S')"
git push
cd - || exit
```

如果可以，可以运行剩下两个组件代码：

- `count.sh`：实现博客字数的统计和自动push。

```bash
#!/bin/bash

# for word counting
bash ../Blog-word-counting/src/run.sh

```

More information can be seem via: [GitHub Repo](https://github.com/xiyuanyang-code/Blog-word-counting)

- `fetching.sh`：实现博客更新状态的自动推送。

```bash
#!/bin/bash

cd ../Blog-Update-Fetching-Script

bash scripts/run.sh

bash scripts/auto_push.sh

cd -
```

More information can be seen via: [GitHub Repo](https://github.com/xiyuanyang-code/Blog-Update-Fetching-Script)