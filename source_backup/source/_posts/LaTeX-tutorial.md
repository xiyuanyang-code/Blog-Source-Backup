---
title: LaTeX-tutorial
date: 2024-11-22 20:01:06
index_img: /img/cover/LaTeX.jpg
excerpt: You will start with an Overleaf template and learn basic LaTeX commands, including text formatting, inserting figures, tables, hyperlinks, and citations, enabling you to handle LaTeX quickly.
categories:
  - Efficient Tools
tags:
  - LaTeX
  - Finished
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
> 好稚嫩的文笔，笔者自己都看不下去... 仅留作纪念。

# About LaTeX

​	听说过LaTeX吗？亦或是老师突然要求你们使用LaTeX进行写论文，自己面对奇奇怪怪的复杂代码毫无头绪，下不去手？

**需要提前准备的内容**：

- 无

**如果你是**：

- 纯LaTeX新手，从零开始接触LaTeX；
- 纯路人，想学习LaTeX技巧；
- 想要支持作者的帅哥美女❥(^_-)；

那恭喜你来对地方了！在这篇文章中，**你将学习到以下内容**：

- 一段LaTeX的历史和一位伟人；
- 掌握LaTeX的最基本原理；
- 如何从零开始构建第一篇LaTeX文档，并按需进行个性化的修改；
- 尝试接触一些LaTeX的高级功能（Optional）；
- 获得一些精进LaTeX技能包的方法和途径；

**准备好了吗？** 在接下来的时光里，就让我们一起走进LaTeX的世界。

![LaTeX](LaTeX.png)

## 参考文献

[TeX - Wikipedia](https://en.wikipedia.org/wiki/TeX)

[LaTeX - Wikipedia](https://en.wikipedia.org/wiki/LaTeX)

[Overleaf, Online LaTeX Editor](https://www.overleaf.com/)

[LaTeX - A document preparation system](https://www.latex-project.org/)

## 1 Introduction

**又来到了讲故事环节，可跳过**

​	在开始我们今天的话题之前，我想先向各位介绍一位老先生，[Donald Knuth](https://en.wikipedia.org/wiki/Donald_Knuth)。

![Donald Knuth](Knuth.jpg)

​	先来看看他到底有多牛逼，这是维基百科对这位老爷爷（还没有去世哦）的评价：He is the 1974 recipient of **the [ACM Turing Award](https://en.wikipedia.org/wiki/Acm_Turing_award)**, informally considered the [Nobel Prize](https://en.wikipedia.org/wiki/Nobel_Prize) of computer science. Knuth has been called the "**father of the [analysis of algorithms](https://en.wikipedia.org/wiki/Analysis_of_algorithms)**"。 [原文](https://en.wikipedia.org/wiki/Donald_Knuth)

​	ACM图灵奖不必多说，能拿奖的都是计算机科学领域大牛中的大牛，他甚至还有第二个称号，**the father of the analysis of algorithms**, 算法分析之父！更不可思议的是，他的主要贡献竟然是一系列至今尚未完篇的鸿篇巨著：*[The Art of Computer Programming](https://en.wikipedia.org/wiki/The_Art_of_Computer_Programming)*。**计算机程序设计艺术**，简称TAOCP，是关于计算机程序设计之七卷本著作。作者并因此获得美国计算机协会1974年[图灵奖](https://zh.wikipedia.org/wiki/图灵奖)。

> 对我来说，读完这本书不仅花了好几个月，而且还要求我有极高的自律性。如果你能读完这本书，不妨给我发个简历。——Bill Gates

​	这一套书涵盖了计算机领域几乎所有的底层知识，目录如下：

![TAOCP](theartofprogramming.png)

[链接指路：计算机程序设计艺术](https://zh.wikipedia.org/zh-cn/计算机程序设计艺术)

​	这本书的具体内容我们不做过多的介绍，那这位老者和LaTeX有什么关系呢？**他是LaTeX的前身——TeX的发明者！**

​	维基百科在这一块写的比较含蓄哈哈哈：

> In 1977, he decided to spend some time **creating something more suitable**. Eight years later, he returned with [TEX](https://en.wikipedia.org/wiki/TeX), which is currently used for all volumes.

什么是**something more suitable**？Maybe the LaTeX！

## 2 LaTeX

​	举一个非常简单的例子，你想在word文档中输入这样一个公式：

![Gradient Descent](QianJianTec1732279187844.png)

> 这是机器学习中梯度下降的基本数学原理。

​	我们来看看word是怎么编辑公式的：

![How does Microsoft Word handle it?](word.png)

​	Word等文字编辑器始终秉持着一种**所见即所得**的思想，我打出一个字符，这个字符就实时的反映在我的文本文档中，至于图片缩放，表格插入，文字大小，排版，以及以上所涉及的数学符号等等，在word中虽然能实现，但是调试的时间成本非常高，难度非常大，而且十分的低效！笔者在高中时期曾经有过写数学小论文卡在编辑公式和统一插入图片的格式等细枝末节的小事上的惨痛的回忆。(不过好像现在word也支持LaTeX了)

​	但是在LaTeX中，我们可以利用下面的代码生成：

```latex
\begin{aligned}
\theta_{j}:=\theta_{j}- & \alpha \frac{\partial}{\partial \theta_{j}} J(\theta) \\
\frac{\partial}{\partial \theta_{j}} J(\theta) & =\frac{\partial}{\partial \theta_{j}} \frac{1}{2}\left(h_{\theta}(x)-y\right)^{2} \\
& =2 \cdot \frac{1}{2}\left(h_{\theta}(x)-y\right) \cdot \frac{\partial}{\partial \theta_{j}}\left(h_{\theta}(x)-y\right) \\
& =\left(h_{\theta}(x)-y\right) \cdot \frac{\partial}{\partial \theta_{j}}\left(\sum_{i=0}^{n} \theta_{i} x_{i}-y\right) \\
& =\left(h_{\theta}(x)-y\right) x_{j}
\end{aligned}
```

​	完全看不懂是不是？没关系，这就是**LaTeX的精髓**：**将可视化的文本编辑转化成一种严谨的代码语言**，当你学会并且快速上手之后，你便会越来越体会到LaTeX在学术写作上的强大之处。

## 3 LaTeX 入门

​	由上面的示例我们可以看出，**LaTeX可以生成极为精美的文档，涵盖公式，表格，图片等等各种形式（你甚至可以用来做PPT！后面会讲到）**，但代价就是**LaTeX牺牲了市面上主流文本编辑器的"所见即所得"的思想**，将所有文字和图片的生成都转化为了一种规则和**编程语言**，初学者入门可能会很吃力，但相信我，熟练后你会爱上他的！

### 	3.1 开始你的第一篇LaTeX文档！

​	万事万物第一步：配环境！一般来说有两种方式：

- 本地环境配置：[TeX Live - TeX Users Group](https://www.tug.org/texlive/)。需要安装textlive，后续可以集成在vscode中使用，具体的操作方法比较繁琐，读者可以自行搜索，网上有很多保姆级的环境搭建教程。（**不推荐，个人建议是等上手熟悉了之后再考虑是否安装，配环境报错真的能把人给烦死**）
- **推荐在线LaTeX网站：**使用LaTeX在线网站，例如[Overleaf](https://www.overleaf.com/)，国内有些高校也会开通自己的LaTeX平台。在线网站更加轻量化，隐藏了配置环境的细节，注册一个账号就可以使用，**非常推荐新手小白使用！**
- 在接下来的新手教程中，我们将全程在[Overleaf](https://www.overleaf.com/)上进行演示，请登录网站并注册好你的账号。（使用免费版即可）

### 	3.2 建立一个模版LaTeX

​	对新手而言，**快速上手熟悉LaTeX比了解LaTeX背后的原理重要的多**。因此，我们将会从一个模版LaTeX出发，从实操慢慢过渡到原理的讲解。

![Choose a template](s1.png)

​	点击左上角的new project——然后有四个选项：

- Blank project（完全的空文本）

- Example Project（模版）

- Upload Project（上传本地项目，通常是压缩包形式）

- Import Project（从Github引入）

  在这里我们选择Example Project。输入自己的Project Name(建议英文)。我在这里输入的是Hello world。

### 	3.3 探索LaTeX模版！

![Hello world!](image_9.png)

​	当你看到一只经典的绿色小青蛙时，恭喜你，你已经成功创建了第一个LaTeX文档！

> 没有看到？试试点一下右上侧绿色的**Recompile**按钮试试看，可能要等一会。

​	简单介绍一下各模块都是干啥用的：

- 最左边：文件栏和**File Outline**
  - 文件栏可以理解为**一个存放这个LaTeX文档所有资源的文件夹**。我们现在中间所打开的就是文件夹中的**main.tex**文件，这也是LaTeX文档的核心部分。还有一些附属的资源也会存放在文件栏中，包括插入的图片，还有参考文献(.bib)。目前大家知道这些就足够了。
  - File Outline 不必多说，展示文章大纲的地方。
- 正中间：文本编辑区，也就是敲代码的地方。
- 最右边：视图区：当你点击**Recompile**的按钮之后，**编译器**（点击左上方的menu可以查看，**新手不要随便改变这一项！**）会根据main.tex的代码以及一些附属文件（例如frog.jpg）生成最终的LaTeX文档（PDF），在右侧可以预览，也可以导出保存。

![The Compiler](image_11.png)

- 上方一栏的一些工具看名字应该就知道是干什么的了，和普通的文本编辑器差不太多，都是一些附加功能，读者可以自己来探索。

### 	3.4 进入main.tex

​	接下来，让我们把重点放在main.tex的代码上。

#### 	3.4.1 整体架构

​	一篇标准的LaTeX代码架构如下：

```latex
% 这是一行注释，不会对代码产生任何影响
\documentclass{article}
%导言区（相当于前期的准备工作）
\begin{document}
%正文（文章内容）
\end{document}
% \end{document}代表着main.tex的结尾，相当于C++的return 0;这个语句后面不要加上任何的东西！（因为加了也没有任何意义）
```

​	`\`在LaTeX中是非常重要的一个字符，类似于C/C++中的转义字符，在后面跟着的都是**LaTeX的关键词和命令**（会有代码高亮），我们**重点学习的内容**就是**这些命令代表什么意思，以及如何使用这些命令**。

​	首先，我们来介绍LaTeX中的**导言区**，即`\begin{document}`之前的部分。

```latex
\documentclass{article}
%这行代码指定了文档的类型为 article，即文章类型。LaTeX 中有几种文档类型（如 report、book 等），article 是最常用的一种，适用于论文、报告、演讲稿等。
```

```LaTeX
% Language setting
% Replace `english' with e.g. `spanish' to change the document language
\usepackage[english]{babel}
% Useful packages
\usepackage{amsmath}
\usepackage{graphicx}
\usepackage[colorlinks=true, allcolors=blue]{hyperref}
```

​	在LaTeX，几乎所有的高级操作都是建立在**导入宏包**的基础之上的，类似于C++中的#include和Python中的import，导入宏包可以让你的LaTeX代码变的更加强大。在此处导入的三个常用宏包分别用于数学公式(amsmath)，图形处理(graphix)和引用(hyperref)。
​	在之后的学习过程中，你可以根据需求自己导入特定的宏包，不过别担心，后面会具体教你怎么做的。

```latex
\title{Your Paper}
\author{You}
% 作者的信息和文章标题，这一块也可以自己魔改（属于高级功能）
```

```latex
\begin{document}
%正文
\end{document}
```

​	夹在`\begin{document}`和`\end{document}`之间的是文章的正文部分，**任何你想在最后PDF出现的内容，无论是图片，文字，都需要在`\begin{document}`和`\end{document}`之间通过命令表达出来！**接下来我们来重点介绍正文部分。

#### 	3.4.2 正文部分①——文字

> 在这里为了方便演示，我创建了一个新的空白文档，所以和LaTeX模版之间存在差异。读者可以先尝试理解这些指令都代表着什么功能，然后回到自己的template中尝试“读懂”他！也可以自己尝试修改一些内容看看最后生成的文档有什么不同~

正文的代码开始看不懂了？别着急，先来看看我写的简化版的

```latex
\begin{document}
\maketitle

\begin{abstract}
Your abstract.
\end{abstract}
\section{introducccction}
\section{Part1}
\subsection{hello}
Hello, this is a test file.
is this a new paragraph?

is this a new paragraph?
\subsection{world!}
\subsubsection{hahah}
\subsubsection{hahahahahah}
i know you are very smart!
\section{Part22}
\begin{enumerate}
    \item this is the firrst point
    \item this is the 2nd points
\end{enumerate}
\section{p3}
\section{Conclusion}

\end{document}
```

包含一些必要的文件准备工作，这份PDF输出如下：

![The Output](image_12.png)

> 为了让展示变得更加清晰，我故意拼错了许多单词，请勿模仿！

- `\maketitle`命令首先输出文章的标题（包括作者信息和时间），不要删除。





- 摘要（abstract）
  - 论文的一个组成部分，由`\begin{abstract}`和`\end{abstract}`包裹。
- `\section`,`\subsection`,`\subsubsection`
  - 类似于论文中1,2,3级小标题的概念，读者可以将代码中的文字和最终输出在PDF中的位置对应起来，应该很快就能够理解。
  - 这就是LaTeX的强大之处，可能用起来没有word可视化，但是他省去了很多文字排版以及大小调整优化的工作，实际上大大地提高了工作效率。

- 正文

  - 文章中*i know you are very smart!* 和*Hello, this is a test file. is this a new paragraph?* 都是正文语句。

  - **缩进**：LaTeX默认模版中首段不缩进，第二段才缩进，如果想更改这个设置，可以在导言区加入命令：

    ```latex
    \usepackage{indentfirst}
    % 这是你加入的第一个宏包！快来compile一下看看有什么变化吧！
    ```

    ![Indient](image_13.png)

    你会发现，第一段也缩进啦！

  - **换行**：

    - 第一个雷点：**在源代码中换行并不代表真的换行了！**比如第一个is this a new paragraph和Hello, this is a test file.分属两行，但他们实际输出上只在一行上面。

    - 最简单的换行方法：**直接在段落中添加一个空行，即按下两个回车键**，这样就能成功实现换行。

      **以下内容新手自动跳过！**

    - 使用`\\`换行，一种更紧凑的写法（但有风险）

    ```LaTeX
    This is the first line.\\
    This is the second line.
    ```

    - 其他换行方法：都是通过一些命令实现，可以作补充了解

    ```latex
    This is the first line.
    \newline
    This is the second line.
    % 类比的，你应该就知道命令
    \newpage
    % 是什么意思了吧！
    ```

    注意，使用`\\`和`\newline`进行强制换行时，两行之间没有额外的垂直间距！格式会有差异，**建议非必要不要使用强制换行符**。

    ![Using Newline Carefully](image_14.png)

    ```latex
    % 一些补充命令：
    
    %有垂直间距的换行
    This is the first line.
    \vspace{1cm}
    
    This is the second line.
    
    %无垂直间距的换行
    This is the first line.
    \noindent
    
    This is the second line without extra space.
    
    ```

    ![Some advanced techniques](image_15.png)

- `enumerate`

  - 中文翻译“枚举”，用于文章中生成小标号，不作为小标题出现

  ```latex
  \section{Part22}
  \begin{enumerate}
      \item this is the firrst point
      \item this is the 2nd points
  \end{enumerate}
  % 注意一个begin对应一个end，不然会报错！
  ```

  ![Enumerate](image_16.png)

  - 可以和一些指令搭配使用，例如加粗`\textbf{}`等等。

- 文本美化：

  > 这里的命令都是比较简单的，大家可以自行尝试~

  - 加粗命令

  ```latex
  \textbf{This text is bold.}
  ```

  - 斜线命令

  ```latex
  \textit{This text is italic.}
  ```

  - 下划线命令

  ```latex
  \underline{This text is underlined.}
  ```

  - 等宽字体

  ```latex
  \texttt{This text is in typewriter font.}
  ```

#### 3.4.3 正文部分②——图片和图表

##### 插入图片

在LaTeX中插入图片需要使用`graphicx`宏包（通常已默认加载）。基本语法如下：

```latex
\begin{figure}[htbp]
    \centering
    \includegraphics[width=0.8\textwidth]{frog.jpg}
    \caption{This is a frog}
    \label{fig:frog}
\end{figure}
```

关键参数说明：
- `[htbp]`：图片位置参数（here, top, bottom, page）
- `\centering`：使图片居中
- `\includegraphics`：实际插入图片的命令
- `width=0.8\textwidth`：设置图片宽度为文本宽度的80%
- `\caption`：图片标题
- `\label`：为图片创建引用标签

##### 插入统计图

对于更复杂的统计图表，推荐使用`pgfplots`宏包：

```latex
\usepackage{pgfplots}
\pgfplotsset{width=0.9\textwidth,compat=1.9}

\begin{tikzpicture}
\begin{axis}[
    title=Example Plot,
    xlabel=Time,
    ylabel=Value,
    xmin=0, xmax=10,
    ymin=0, ymax=10,
    xtick={0,2,4,6,8,10},
    ytick={0,2,4,6,8,10},
    legend pos=north west,
    ymajorgrids=true,
    grid style=dashed,
]

\addplot[
    color=blue,
    mark=square,
    ]
    coordinates {
    (1,1)(2,4)(3,9)(4,16)(5,25)
    };
    \legend{Square Function}
    
\end{axis}
\end{tikzpicture}
```

##### 插入统计表

使用`tabular`环境创建表格：

```latex
\begin{table}[htbp]
\centering
\caption{Example Table}
\label{tab:example}
\begin{tabular}{|l|c|r|}
\hline
Left Aligned & Centered & Right Aligned \\
\hline
Item 1 & 12 & 1.5 \\
Item 2 & 24 & 2.5 \\
Item 3 & 36 & 3.5 \\
\hline
\end{tabular}
\end{table}
```

表格说明：
- `{|l|c|r|}`：定义列对齐方式（左中右）和竖线
- `\hline`：水平线
- `&`：列分隔符
- `\\`：行结束符

#### 3.4.4 正文部分③——数学公式

LaTeX最强大的功能之一就是数学公式排版。数学公式分为两种模式：

##### 行内公式

用`$...$`或`\(...)`包裹：

```latex
The quadratic formula is $x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$.
```

##### 行间公式

用`\[...\]`或`equation`环境：

```latex
\[
E = mc^2
\]

\begin{equation}
f(x) = \int_{-\infty}^\infty \hat f(\xi)\,e^{2 \pi i \xi x} \,d\xi
\end{equation}
```

##### 常用数学符号

```latex
希腊字母：$\alpha, \beta, \gamma, \Gamma$

上下标：$x^2, x_i, x_{i,j}, x^{y^z}$

分数：$\frac{a}{b}, \dfrac{a}{b}$

根式：$\sqrt{x}, \sqrt[n]{x}$

求和/积分：$\sum_{i=1}^n, \prod_{i=1}^n, \int_a^b$

矩阵：
\[
\begin{bmatrix}
1 & 2 \\
3 & 4 \\
\end{bmatrix}
\]

多行公式：
\begin{align}
f(x) &= (x+1)^2 \\
     &= x^2 + 2x + 1
\end{align}
```

##### 定理环境

```latex
\usepackage{amsthm}

\newtheorem{theorem}{Theorem}
\newtheorem{lemma}{Lemma}

\begin{theorem}[Pythagorean]
In a right triangle, the square of the hypotenuse is equal to the sum of the squares of the other two sides.
\end{theorem}
```

## 4 进阶技巧

### 参考文献管理

使用BibTeX管理文献：

1. 创建`.bib`文件
2. 引用文献：

```latex
\cite{key}  % 文中引用
```

3. 在文档末尾添加：

```latex
\bibliographystyle{plain}
\bibliography{references}
```

### 自定义命令

```latex
\newcommand{\R}{\mathbb{R}}  % 定义新命令
\newcommand{\abs}[1]{\lvert #1 \rvert}  % 带参数的命令
```

### 幻灯片制作

使用`beamer`制作幻灯片：

```latex
\documentclass{beamer}

\begin{document}

\begin{frame}
\frametitle{First Slide}
Content of the first slide
\end{frame}

\end{document}
```

# 5 学习资源推荐

1. [Overleaf文档](https://www.overleaf.com/learn)
2. [LaTeX Wikibook](https://en.wikibooks.org/wiki/LaTeX)
3. [Detexify符号识别工具](http://detexify.kirelabs.org/classify.html)
4. [CTAN宏包仓库](https://www.ctan.org/)
