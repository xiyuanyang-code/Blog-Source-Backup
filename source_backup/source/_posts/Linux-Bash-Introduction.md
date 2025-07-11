---
title: Linux-Bash-Introduction
date: 2025-01-13 22:31:11
index_img: /img/cover/bash.jpg
excerpt: In this blog, you will first learn what the Linux is and begin to explore Bash, along with its powerful applications in Linux. You can also try writing some Bash scripts to achieve automation.
categories:
  - Efficient Tools
  - Missing Semesters
tags:
  - Bash
  - Linux
  - Finished
  - tutorial
---

封面出处：[Bash and Linux](https://www.makeuseof.com/linux-bash-what-use-for/)

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Linux and Bash Introduction

![Tux the penguin](https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/150px-Tux.svg.png)

> 思来想去还是用中文写吧。。。全英文的博客写作还需要一段时间来适应。
>
> 中文敲字是真的快。。。

## Introduction

当你在网上下载软件时，你可能会看到这样的页面：

<center><img src="https://s1.imagehub.cc/images/2025/02/14/590ddd670394edd6af67cae090b2d6fb.png" alt="Screenshot 2025 02 14 140426" border="0"></center>

**Windows** 和 **MacOS** 相信大家并不陌生，但中间这个有点蠢蠢的小企鹅是什么？这便是大名鼎鼎的 **Linux** 系统，本文将会介绍 **有关Linux最基本的知识**，以及从 **Linux** 延伸到 **Bash**等命令行工具，提供为CLI新手的导引。

## What is Linux

和 **Windows** 与 **MacOS**一样，**Linux**是一种 **操作系统**。（这里主要涉及 **桌面操作系统**，Linux也可以用于服务器部署和嵌入式开发等领域，此文不涉及）

**什么是操作系统**？官方定义：

> **操作系统**是是**管理计算机硬件和软件资源的系统软件**，它是计算机系统的核心组成部分。操作系统为用户和应用程序提供了一个与硬件交互的接口，同时负责管理计算机的资源，确保系统高效、稳定地运行。它的作用主要包括：
>
> - **进程管理**
> - **内存管理**
> - **文件系统管理**
> - **设备管理**
> - **用户界面**
> 	- 命令行界面（CLI）
> 	- 图形用户界面（GUI）

换句话说，**Operating System**就像管家，负责管理计算机的**硬件资源**并与 **软件程序**沟通，让用户更方便的操作并使用计算机。通俗来说，你只需要使用电脑上的软件（例如点击图标，打字等），而不需要查看每一根内存条的使用情况，因为操作系统帮你完成了这一步。

**Linux** 就是一个类 **Unix** 的操作系统，和Windows系统有很大的差异。

### Philosophy of Linux

Linux的哲学是Linux操作系统设计和使用的核心理念，它源于**Unix哲学**，并在此基础上发展出独特的思想体系。

> **Philosophy of Unix** (from Wikipedia[^2])
>
> The **Unix philosophy**, originated by [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson), is a set of cultural norms and philosophical approaches to [minimalist](https://en.wikipedia.org/wiki/Minimalism_(computing)), [modular](https://en.wikipedia.org/wiki/Modularity_(programming)) [software development](https://en.wikipedia.org/wiki/Software_development). It is based on the experience of leading developers of the [Unix](https://en.wikipedia.org/wiki/Unix) [operating system](https://en.wikipedia.org/wiki/Operating_system).
>
> 
>
> 1. Make each program **do one thing well**. To do a new job, build afresh rather than complicate old programs by adding new "features".
> 2. **Expect the output of every program to become the input to another**, as yet unknown, program. Don't clutter output with extraneous information. Avoid stringently columnar or binary input formats. Don't insist on interactive input.
> 3. Design and build software, even operating systems, to be tried early, ideally within weeks. Don't hesitate to throw away the clumsy parts and rebuild them.
> 4. Use tools in preference to unskilled help to lighten a programming task, even if you have to detour to build the tools and expect to throw some of them out after you've finished using them.

Linux哲学的核心实现和Unix哲学差不了太多，即 **简化** 的思想渗透到方方面面，下面我们来逐条解释：

#### ✨一切皆文件

Linux系统将几乎所有资源都抽象为**文件**，包括硬件设备、进程、网络连接等。这种设计使得用户可以通过统一的文件操作接口（如读写、权限管理）来管理系统的各种资源。例如：

- 硬件设备：`/dev/sda` 表示硬盘设备。
- 进程信息：`/proc/` 目录下存储了运行中进程的信息。
- 网络连接：`/etc/hosts` 文件存储了主机名和IP地址的映射。

这种设计简化了系统的复杂性，使得用户可以通过简单的命令行工具（如`cat`、`echo`）来操作各种资源。

**这是非常重要的一点！**例如，很少有人会去C盘查看并且魔改自己的系统盘（当然很大一方面是因为Windows操作系统是闭源收费的），但是在Linux系统下，一切都被规整地以文件的形式摆放，**你甚至可以使用命令行控制硬件！（例如调节屏幕亮度等与硬件直接关联的操作）**。

#### ✨小即是美

Linux鼓励使用小而专注的工具，每个工具只完成一个特定的任务，但可以高效地组合起来完成复杂的工作。例如：

- `ls` 用于列出目录内容。
- `grep` 用于搜索文本。
- `awk` 用于处理文本数据。

通过**管道**（`|`）将这些工具组合起来，可以实现强大的功能。

> 这与Unix哲学的第二条不谋而合。

#### 模块化设计

Linux系统由许多独立的模块组成，每个模块负责特定的功能。这种设计使得系统易于维护和扩展。例如：

- 内核模块可以动态加载和卸载。
- 用户可以**根据需要安装或卸载软件包**。

在Windows系统下，安装软件往往需要繁杂的**安装向导**步骤，但是在Linux系统下，几乎一条`sudo apt install XXX`就可以搞定一切安装。

#### 自由与开源

**Linux** 是开源且免费的操作系统，**所有源代码公开且任何人可以查看，修改，分发**。

开源精神，伟大无需多言！

![Linus-Nvidia-fuck-you-portada2](https://data.crazyengineers.com/old-attachments/3/3041-Linus-Nvidia-fuck-you-portada2.jpg)

#### ✨命令行优先（避免令人困惑的操作界面）

这一点是对Linux新手来说最重要的一点，也是最不适应的一点。（对于初学者）

举个例子，假设Xiyuan Yang需要制作PPT，他可能会使用 **Powerpoint** 或者 **Slides**进行幻灯片制作，效率很高，也很巴适。

但是情况并非如此，例如，Xiyuan Yang需要新建一份txt文件来储存自己的OpenAI的API密钥，如果他在Windows系统上，会需要哪几步呢？

> - 首先在合适的目录下面新建一个新的目录
> - 将新的目录重命名为`OpenAI_API_key`
> - 右键选择“新建”，找到“.txt”文件选项，命名为`API_key_1`
> - 使用文本编辑器打开这个文件，鼠标右键点击**粘贴**
> - 点击保存，然后退出文件

这个过程看似在30s内就能完成，但实际上还是相对比较繁琐，或者说，**仍然有简化的余地**。

什么叫 **简化的余地**？对于绝大部分时间，我们与电脑都是使用 **图形交互页面** 进行交互，我们在 **可视化的电脑屏幕** 上通过移动、点击鼠标或者键入键盘来实现我们需要的操作。**图形界面**意味着 **更形象化的表示但同时带来冗余的信息**。例如在上面的例子中，当你点击一个文件夹，GUI会显示这个文件夹中的所有文件（夹），这确实更加直观，但是**显示的其他文件夹**是冗余的信息，你并不想要看见他！（在这个任务中）举个更极端的例子，当你电脑开机，看见桌面的第一眼，信息的冗余就开始产生了，毕竟你还什么都没有做，就已经看见了放在桌面上的“多余信息”。

因此，**Linux**的设计哲学的核心之一就是 **避免令人困惑的操作界面**：更细致地来说就是**使用命令行界面，同时显示极简的信息但又不失工作效率**。通过命令行，用户可以精确控制系统，并编写脚本自动化任务。

这是我的Linux命令行页面，只会保留最基本的时间等基本信息。

<img src="https://s1.imagehub.cc/images/2025/02/14/ba665b70f5a9490a8cb66cd3ede32390.png" alt="Screenshot 2025 02 14 235049" border="0">

对于上面的任务，在Linux中只需三行命令就可搞定：

```Bash
mkdir Bash_exercise/syntax/Test_for_openAI_API

ls Bash_exercise/syntax/Test_for_openAI_API

vim API1.txt
```

#### 避免重复造轮子

这点对开发者来说很重要，使用现成的工具提高工作效率，把更多的精力专注于项目本身，这一点不多解释。

### Linux Kernel and Releases

上文提到过，**操作系统最核心的功能之一就是和硬件进行交互**。在Linux系统中，这个部分叫做**内核**。

> 对于Linux新手，并不需要对Linux的内核有过多深入的了解。此处略过

**Linux**内核相当于一个操作系统的心脏，也是核心部分，但单单有一个心脏是不可行的，因此，Linux操作系统在内核之外，还需要预安装一些软件包和工具。在安装好之后便可以发行给大众使用了，而**安装的软件包和工具**，不同的开发者可以设计自己的软件包，因此便有了**不同的Linux发行版**。

以下是一些常见的Linux发行版：

- **Ubuntu**：适合初学者，拥有庞大的社区支持。
- **Fedora**：由Red Hat支持，专注于新技术的采用。
- **Debian**：以稳定性和自由软件著称，是Ubuntu的基础。
- **CentOS**：基于Red Hat Enterprise Linux（RHEL），适合企业服务器。
- **Arch Linux**：面向高级用户，强调定制性和简洁性。

### Why Linux？

你说的对，但为什么我们要去使用Linux系统？

在半年前，当笔者第一次装好了虚拟机（现在换成WSL了），并且输入第一个`ls`命令的时候，脑中便不由自主地冒出这个问题。对于一个资深的开发者，这个问题简直就是玩笑话，他可以随意地列举出无数Linux在开发中优于Windows的理由，有关三大操作系统的鄙视链之争吵一直也没有停歇[^1]。

因此，对于新手，对这个问题一直揪着不放是没有任何意义的？或者换句话说，**对于绝大多数Linux的使用者**，了解Linux的使用远比了解Linux的原理要重要得多。**在上手实践中**，你便会体会到Linux的强大之处。

**This is the art of the command line**！

## Bash and Shell

### What is Shell?

**Shell** 是 Linux 和类 Unix 操作系统中的一个核心概念，它是**用户与操作系统内核之间的桥梁**。简单来说，Shell 是一个**命令行解释器**，它接收用户输入的命令，并将其传递给操作系统内核执行，然后将结果返回给用户。

在 Linux 中，有多种不同的 Shell，常见的有：

- **Bash（Bourne Again Shell）**：最流行的 Shell，大多数 Linux 发行版的默认 Shell。
- **Sh（Bourne Shell）**：早期的 Shell，Bash 的前身。
- **Zsh（Z Shell）**：功能强大的 Shell，支持更丰富的自动补全和主题配置。
- **Ksh（Korn Shell）**：结合了 Bash 和 C Shell 的特性。
- **Csh/Tcsh（C Shell）**：语法类似于 C 语言，适合有编程经验的用户。

当然，Shell的概念也不只局限于类Unix系统，例如Windows系统的Powershell。

## Bash Scripts

上文介绍了Shell是命令行解释器，而如果我们希望实现**自动化办公**，我们就需要让Shell自动解释我们提前编写好的命令，这样就不用每一次都手动输入了。

**Bash 脚本**（Bash Scripts）是用 Bash（Bourne Again Shell）编写的脚本文件，用于自动化执行一系列命令和任务。Bash 是 Linux 和类 Unix 系统中最常用的 Shell，而 Bash 脚本则是利用 Bash 的功能来实现复杂的操作、批量处理任务或系统管理。Bash 脚本的本质是一个**文本文件**，其中包含一系列 Bash 命令和控制结构（如条件判断、循环等），通过运行这个脚本文件，系统会逐行执行其中的命令。

> 可以把Bash看做一门编程语言来使用！

有关Bash脚本编写的具体内容见后期更新~

# Memo for Bash

> This section is being updated during my learning process of bash scripts.

## Shortcut in Bash

### **Cursor Movement**

- **Ctrl + A**: Move the cursor to the beginning of the line.

- **Ctrl + E**: Move the cursor to the end of the line.

- **Alt + B**: Move the cursor back one word. or **Ctrl + <-**.

- **Alt + F**: Move the cursor forward one word.or **Ctrl + ->**.

- **Ctrl + XX**: Toggle between the beginning of the line and the current cursor position.

### **Editing Commands**

- **Ctrl + U**: Cut text from the cursor to the beginning of the line.

- **Ctrl + K**: Cut text from the cursor to the end of the line.

- **Ctrl + W**: Cut the word before the cursor.

- **Alt + D**: Cut the word after the cursor.

- **Ctrl + Y**: Paste the last cut text.

- **Ctrl + T**: Swap the last two characters before the cursor.

- **Alt + T**: Swap the current word with the previous word.

### **Command History**

- **Ctrl + P**: Move to the previous command in history (same as Up Arrow).

- **Ctrl + N**: Move to the next command in history (same as Down Arrow).

- **Ctrl + R**: Search command history interactively.

- **Ctrl + G**: Exit the history search mode.

### **Process Control**

- **Ctrl + C**: Terminate the current process.

- **Ctrl + Z**: Suspend the current process.

- **Ctrl + L**: Clear the terminal screen.

- **Ctrl + D**: Exit the shell or close the terminal.

### **Miscellaneous**

- **Ctrl + S**: Pause terminal output (useful for stopping fast-scrolling text).

- **Ctrl + Q**: Resume terminal output after pausing.

- **Ctrl + _**: Undo the last change.

- **Alt + .**: Insert the last argument of the previous command.

### **Tab Completion**

- **Tab**: Auto-complete file names, directories, or commands.

- **Double Tab**: Show all possible completions.



# References

[^1]: https://www.zhihu.com/question/295142135
[^2]: https://en.wikipedia.org/wiki/Unix_philosophy
