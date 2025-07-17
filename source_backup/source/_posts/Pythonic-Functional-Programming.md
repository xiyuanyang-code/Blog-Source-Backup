---
title: Pythonic Functional Programming
date: 2025-07-16 12:18:17
index_img: /img/cover/functionpro.jpg
math: true
excerpt: "Tutorial for Pythonic: the introduction of functional programming and its philosophy"
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Pythonic
  - Python
---

# Functional Programming

## Coding Style

**编程范式**是相比于编程语法规则更加高级并且抽象的概念，他规范并且指导程序员如何组织规范的代码。编程范式的设计往往和语言本身的特性与应用场景息息相关，例如：

- **面向过程的编程范式**：比较低级且简单的编程范式，将程序分解为一系列的“过程”或“函数”，这些过程按照顺序执行，共同完成任务。数据和操作数据的函数是分离的。

> 以下两种编程方式依赖于特定的语言和应用场景

- **声明式编程**：关注“做什么”而不是“怎么做”。程序员描述期望的结果，而不是详细的步骤。系统会自动找到实现结果的方法。例如Coq，SQL，HTML/CSS

- **事件驱动编程**：程序流程由用户操作、传感器输出或其他程序的“事件”决定。程序响应这些事件来执行相应的处理。例如JavaScript (`event.onClick()`)

> 对于更广泛的软件工程而言，以下的两种编程范式是**较为主流且被广泛采纳的**：

- **面相对象编程**：核心在于不同层级的类的继承，封装以及接口的设计。将程序视为一组相互协作的“对象”。每个对象封装了数据（属性）和操作数据的方法（行为）。

- **函数式编程**：将程序视为一系列纯函数的求值过程。强调使用函数来转换数据，而不是改变数据的状态。

> 纯函数式编程的覆盖面叫窄，但是包括很多语言都提供了函数式编程的范式，学习函数式编程的思想对我们有很大的好处。

