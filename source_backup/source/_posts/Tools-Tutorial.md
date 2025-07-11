---
title: Tools-Tutorial
date: 2025-01-05 09:38:42
index_img: /img/cover/tools.png
excerpt: In this article, you will learn a set of methodologies that teach you how to master the use of tools through practical approaches, while continuously improving your skills through ongoing practice.
categories:
  - Efficient Tools
tags:
  - Methodologies
  - tutorial
  - Finished
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Tools Tutorial for Computer Science

[**封面出处**](https://developerguru.in/full_stack_development.php)

## Abstract

In this article, you will learn a set of **methodologies** for using tools in computer science. It will teach you how to quickly get started and master the use of tools through **practical approaches**, while continuously improving your skills through ongoing practice.

## Table of contents

## Introduction

程序员之间很流行一句话，**不要重复造轮子**。

> 造轮子其实是个很值得探讨的话题，有机会日后开一篇聊一聊~

的确，作为轮子的**使用者**，我们不需要过分关注开发者所封装好的具体的实现细节，只需要掌握工具最基本的使用手段，就可以最大化地提升自己的效率。

然而，在实际的学习过程中时，笔者经常会为**工具的学习**而感到苦恼，比如：

- 在`Github`上淘到一个很有意思的project，想开箱玩一玩，但是止步于**读懂用户手册**。
- Be trapped in 各种讨人厌的配置环境和报错信息中。
- 尝试学习一项新技能，但发现这项技能需要许多的**前置技能**，导致迟迟无法获得学习的正向反馈，最后学习的热情被浇灭不了了之。
- 学习曲线过于陡峭，导致在使用初期开发效率**不升反降**。
- 学会了但总是没有养成经常使用的习惯，导致学了忘忘了学，每次需要一些命令还得很麻烦的问`GPT`。

日后，在与其他同学的交流中，发现这样的问题并非个例，而是普遍存在的问题！在下文中，笔者将以`git`的学习历程为例，手把手搭建工具学习的高效方法论。在博客的最后，笔者将会对方法论作系统性的总结，并对上文提到的5个问题做针对性的解答。

## Basic Principles

用12字概括这一套方法论，就是：

<p><div style="width: 80%; margin: auto;"><strong style="color: red;
    font-size: 30px;
    "><b>提高效率，二八法则，基于实践。</b></strong></div></p>


- 提高效率：这是**工具使用的目的**。
- **二八法则**：这其实还是**提高效率**的另一种表达：花较短的时间学习工具20%的功能，便可以解决80%的问题。
- **实践为王**：工具使用的落脚点不是纸上谈兵而是具体落地到项目中去使用。

## Showcasing Usage with Git

### Overall Perception of Basic information

<p><span style="font-size: 14pt;"><strong><font  color="red">感知新内容的基本信息&amp;初步构建较为完整的知识网络</font></strong></span></p>

学习新技能的第一步，就是**很好的认识他**！

这一点其实非常重要，而恰恰被网上天花乱坠的保姆级无脑手把手教程所忽略了，就是**这个工具是怎么来的？为什么我们需要这一个工具？ **这涉及到工具的根本属性：**改善工作流，提高生产力**。一切工具的使用都是基于这一点而出发的，换句话说，工具的使用都是为了**解决现实生活中的实际问题**。

因此，认识工具的第一步就是回答下面三个问题：

- 这个工具是什么？用一句话概括展现其本质的属性。
- 这个工具是为了解决什么实际问题而存在的？
- 这个工具有什么用处？

新的问题来了，我们该如何高效地获取上面这三个问题的答案？下文笔者将以`Git`做示范。

#### What is Git?

如何迈出我们学习的第一步？那当然是从**网上获取答案**。在这一步，我们需要重点关注<strong><font  color="red">信息渠道来源的权威性和可解释性</font></strong>。下面分别阐述这两点。

##### The authority of information

 我们上网搜索`Git`，以下三张图展示了不同搜索引擎的搜索结果：

![Google Search](https://ooo.0x0.ooo/2025/01/11/OE9RXx.png)



![Bing Search](https://ooo.0x0.ooo/2025/01/11/OE94xj.png)



![Baidu Search](https://ooo.0x0.ooo/2025/01/11/OE9DJp.png)


我们发现很多全新的名词，**现在看不懂没有关系**，但至少我们发现了一个网站，https://git-scm.com ，这个网站在三个搜索引擎的页面都出现了，足以证明其重要性。

> 搜索引擎的选择其实也有很多学问，笔者的默认搜索引擎和浏览器是**Bing**浏览器，有时也会用Google浏览器（这个有时候需要一些魔法，个人感觉在日常使用上Bing已经完全足够了）。**非常不建议各位使用百度或者360等软件作为主力搜索引擎去使用**，广告弹窗太多并且推荐系统很垃圾。就像在上面展示的，百度的首个推送竟然是广告，并且也没有像Bing或Google这样的首页摘要供用户快速查询。

打开这个网站，我们发现了新世界。

这就是**Git**的官方网站，在这里的信息代表着**最新颖最权威**的信息！没有经过任何转述的过程。这也是我们后续学习的**主战场之一**。

![Git](https://ooo.0x0.ooo/2025/01/11/OE9EBY.png)

细看这个网站，貌似我们能够回答第一个问题了，**这个工具是什么？**`Git`的官网给出了最权威并且也是最准确的解答：

> Git is a [free and open source](https://git-scm.com/about/free-and-open-source) **distributed version control system** designed to handle everything from small to very large projects with speed and efficiency.
>
> Git is [easy to learn](https://git-scm.com/doc) and has a [tiny footprint with lightning fast performance](https://git-scm.com/about/small-and-fast). It outclasses SCM tools like Subversion, CVS, Perforce, and ClearCase with features like [cheap local branching](https://git-scm.com/about/branching-and-merging), convenient [staging areas](https://git-scm.com/about/staging-area), and [multiple workflows](https://git-scm.com/about/distributed).
>
> 贴心的附上中文翻译，不过学好英语还是很重要的哇！
>
> Git 是一个[免费且开源](https://git-scm.com/about/free-and-open-source)的**分布式版本控制系统**，旨在以速度和效率处理从小型到超大型的各种项目。
>
> Git [易于学习](https://git-scm.com/doc)，并且具有[极小的资源占用和闪电般的性能](https://git-scm.com/about/small-and-fast)。它通过诸如[廉价的本地分支](https://git-scm.com/about/branching-and-merging)、便捷的[暂存区域](https://git-scm.com/about/staging-area)和[多种工作流程](https://git-scm.com/about/distributed)等特性，超越了 Subversion、CVS、Perforce 和 ClearCase 等 SCM 工具。

{% note success %}

**锦囊妙计①：重视官方网站（权威性）**

{% endnote %}

##### The interpretability of information

这看着很酷，不是吗？毕竟我们找到了一个“宝藏网站”，也知道了后续我们要在哪里学习。但是，**Git**的官方网站算是世界上写的**最好的官方文档之一了**，实际上很多项目的官方文档并不具备很高的**可读性**。

比如，下面是C++的官方文档，当时真的是对我幼小的心灵造成了降维打击。像这样的官方文档就会更加注重**全面性和严谨性**，受众群体是准专业开发者而非入门的新手菜鸟。而对于菜鸟来说，这个网站更多应该起到一种**References**的作用。

![C Plus Plus](https://ooo.0x0.ooo/2025/01/11/OE90Mc.png)

> 程序员最讨厌的四件事：写注释、写文档、别人不写注释、别人不写文档。‌

又比如，其实你并不了解上文的**distributed version control system**到底是啥意思？这其实是因为我们缺乏一种**可读性且直观性的解释**。而**生成式AI模型**可以给我们带来生动形象的解释（信息的可解释性）。顺带的，我们也可以解决我们在最开始问的第二个问题，**这个工具是为了解决什么实际问题而存在的？**

在这里各位不需要为AI工具的使用而发愁，我推荐使用GPT，Claude（这俩需要魔法），国内推荐**DeepSeek，豆包，文心一言，天工AI等**。笔者目前使用`GPT+Cursor+DeepSeek`的组合。下文的实例都将使用DeepSeek作为示范AI：

{% note primary %}

**ChatBox with DeepSeek**

**Me**: 请用通俗的语言解释一下Git是什么

---

**DeepSeek**: Git 是一个版本控制系统，简单来说，它就像是一个“时光机”，帮你记录和管理文件的变化。

举个例子：假设你在写一篇文章，每次修改后，Git 都会帮你保存一个“快照”。这样，如果你改错了内容，或者想看看之前的样子，Git 可以轻松帮你找回之前的版本。

Git 还特别适合多人协作。比如你和朋友一起写代码，Git 可以让你们同时工作，最后再把各自的修改合并到一起，避免混乱。

总结一下，Git 的主要功能就是：
1. 记录文件的变化历史。
2. 方便多人协作。
3. 随时回退到之前的版本。

它广泛用于软件开发，但也可以用来管理任何类型的文件。

---

**Me**: Git在实际应用中会解决什么样的问题？

---

**DeepSeek**: Git 在实际应用中主要解决以下几类问题：

1. **版本管理**

   - **问题**：在开发过程中，文件会不断修改，可能会遇到改错代码、误删文件，或者想找回之前某个版本的情况。
   - **Git 的解决方案**：Git 会记录每次文件的变化，你可以随时查看历史版本，甚至回退到之前的某个状态。

   **例子**：你写了一段代码，后来改坏了，可以用 Git 轻松找回之前的正确版本。


2. **多人协作**

   - **问题**：多人同时修改同一个文件时，容易产生冲突，或者不知道谁改了哪里。
   - **Git 的解决方案**：Git 允许每个人在自己的分支上工作，最后将修改合并到一起。如果有冲突，Git 会提示你解决。

   **例子**：你和同事同时开发一个功能，Git 可以帮助你们合并代码，避免覆盖对方的工作。


3. **代码备份**

   - **问题**：代码只存在本地电脑上，如果电脑坏了或文件丢失，工作成果就没了。
   - **Git 的解决方案**：Git 可以将代码上传到远程仓库（如 GitHub、GitLab），实现备份和共享。

   **例子**：你可以把代码推送到 GitHub，这样即使电脑出问题，代码也不会丢失。


4. **分支管理**

   - **问题**：在开发新功能或修复 bug 时，直接修改主代码可能会影响正在运行的程序。
   - **Git 的解决方案**：Git 支持创建分支，你可以在新分支上开发，完成后再合并到主分支。

   **例子**：你想开发一个新功能，可以创建一个新分支，完成后测试无误再合并到主分支。


5. **追踪改动**

   - **问题**：修改代码后，很难记住具体改了哪些地方，为什么要改。
   - **Git 的解决方案**：Git 会记录每次改动的具体内容，并且你可以为每次改动添加注释（commit message），方便以后查看。

   **例子**：你可以通过 Git 查看某次改动的具体内容，了解为什么修改了某段代码。

6. **代码审查**

   - **问题**：多人协作时，如何确保代码质量？
   - **Git 的解决方案**：通过 Git 的 Pull Request（PR）功能，其他人可以查看你的代码改动并提出建议。

   **例子**：你完成一个功能后，发起 PR，同事可以检查代码并提出改进意见。


**总结**

Git 主要解决了以下问题：
- 版本丢失或混乱。
- 多人协作时的冲突。
- 代码备份和共享。
- 开发新功能时的风险隔离。
- 追踪改动和代码审查。

无论是个人项目还是团队协作，Git 都是提高效率、降低风险的重要工具。

---

**Me**: Git的官网上解释的是distributed version control system，你能用通俗的语言解释一下吗

---

**DeepSeek**: 当然可以！Git 官网说它是一个 **“分布式版本控制系统”**，听起来有点复杂，但其实很简单。我们可以拆开来看：

1. **版本控制系统**

Git 是一个用来管理文件变化的工具。比如你写代码或文档，每次修改后，Git 都会帮你保存一个“快照”。这样，你可以随时查看之前的内容，或者回退到某个历史版本。

2. **分布式**

这是 Git 最特别的地方。传统的版本控制系统（比如 SVN）是 **集中式** 的，所有文件的历史版本都存放在一个中央服务器上。如果服务器坏了，大家就没法工作了。

而 Git 是 **分布式** 的，意思是：
- 每个人电脑上都有一个完整的仓库（包括所有文件的历史记录）。
- 你不需要依赖中央服务器，可以在本地完成大部分操作（比如查看历史、创建分支、提交改动等）。
- 你可以随时和其他人的仓库同步，交换改动。

举个例子：

假设你和朋友一起写代码：
1. 你用 Git 把整个项目（包括所有历史记录）复制到自己的电脑上。
2. 你在本地修改代码，Git 帮你记录每次改动。
3. 你朋友也在他的电脑上修改代码，Git 也帮他记录。
4. 你们各自完成后，可以把改动同步到一起。

即使没有网络，你也可以在本地工作，等有网了再同步。如果中央服务器坏了，你们每个人的电脑上都有完整的备份，不会丢失数据。

总结

“分布式版本控制系统” 就是：
- **版本控制**：帮你记录文件的变化历史。
- **分布式**：每个人电脑上都有完整的仓库，不依赖中央服务器，更灵活、更安全。

Git 的这种设计让它特别适合个人开发、团队协作，甚至是开源项目的全球协作（比如 Linux 内核开发）。

{% endnote %}

我在这里问了AI三个问题，**他直接解决了我的所有问题！！！**可谓是强大至极。我不仅知道了Git官网中的“分布式版本控制系统”是什么意思，还知道了Git在日常用途中经常使用在哪些场景中，Git能够解决哪些生活中的实际问题。

{% note success %}

**锦囊妙计②：善用AI工具（可解释性）**

当你束手无策的时候，不妨和AI聊聊天，对于入门级别的工具教程而言，AI已经能够做的比大部分人类教程要好了。

{% note danger %}

注意，不要产生对**AI**的过度依赖心理！AI的输出并不保证绝对的正确性，尤其是在涉及更深层次问题的时候。因此，**极度不推荐**完全借助AI的力量来学习工具！这往往会带来很严重的隐患，作为一个学习者的身份，你根本无法判断AI生成的内容到底是正确的还是胡说八道。相反，将AI作为工具辅助学习才是最为高效且安全的选择。

{% endnote %}

{% endnote %}

至此，以下三个问题的答案，已经如何找到以下三个问题答案的答案，已经被我们找到了：

- 这个工具是什么？用一句话概括展现其本质的属性。
  - 分布式版本控制系统，记录文件变化历史。
- 这个工具是为了解决什么实际问题而存在的？
  - 管理文件版本，避免丢失，支持多人协作。
- 这个工具有什么用处？
  - 备份代码、回退版本、分支管理、团队协作。

{% note info %}

**Period 1:** 学习最基本的框架知识，经典三连问。

**学习时间**：<1h（熟练起来20分钟足矣）

**关键**：

- 把握官方网站和一些权威途径（权威性）
- 善于使用AI工具（可解释性）

{% endnote %}

### Quick Start for a project

- 借助他人使用的轮子的力量

- 借助典型示例快速上手一个project

- **最重要的部分**

  <p><span style="font-size: 18pt;"><strong><font  color="red">以实战项目为最好的教材，在实践中学习代码（知识）是怎么实现的</font></strong></span></p>

在掌握最基本的概念之后，我们需要**使用这个工具**，这也是一整套方法论中最关键的部分：**实践永远放在第一位**！

#### Which materials to choose?

最初的学习总是从**模仿**开始的，而一个**从零开始**的教程往往更加重要。

### From imitating to modifying and enhancing

- 在精读完示例project后，可以尝试开始 **模仿**
- **模仿————修改————优化**
- 不断地发现问题，解决问题！

### Diving into principles

- 在这个阶段，已经初步具备构建工程的基本能力，但仍需要进一步掌握**底层原理和一些更高级更进阶的用法（自主性和创新性）**
- 可以在开源网站上学习他人的成果
- 尝试阅读官方文件或者一些底层文件
- 对底层原理的学习往往是枯燥的，**对于工程实践来说初期永远是实践最重要，对于原理层面起到锦上添花的作用，目的在于为我们后续的个性化创新奠定基础。**

### Occasional Backtracking

- **经典回读**！！！
- 1-2-3-4往往是循环的过程，在哪一步都会受挫，理性分析，适当求助，疯狂拷打ChatGPT

## Conclusion and Notice

- 这一套方法论并不代表不重视**基础原理**，相反，如果你想要做出真正自己的原创，肯定还是需要熟练掌握底层原理（至少大部分）的。
- 先有**Intuition**，再有**Implementation**，最后再慢慢填补剩下的原理等层面，不断的产生新的感悟。
- **不要怕重复训练**，很多事情在做第二遍第三遍是收获的效果要远远高于第一遍。
- **不要过度依赖AI**，自身能力的捶打是需要自己不断实现的，参考 [这一篇文章](https://nmn.gl/blog/ai-and-learning)。

## References

[Git documentation](https://git-scm.com/docs)



<p style="text-align: center;">
    <span style="font-size: 30pt;">
        <strong>
            <font  color="red">
                Be a Life-long Learner
            </font>
        </strong>
    </span>
</p>
