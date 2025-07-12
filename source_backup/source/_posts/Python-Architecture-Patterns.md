---
title: Python Architecture Patterns
date: 2025-07-12 15:24:51
excerpt: Introduction to architecture design patterns in Python, with several announcement of advanced Python programming.
index_img: /img/cover/architecture_pattern.jpg
categories:
  - Code
  - Architecture Design
tags:
  - finished
  - Tutorial
  - Architecture Patterns
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Architecture Patterns in Python

## Introduction

- In the class of "Introduction to Programming", we learn how to **write code in a right way**, which means your code will output the right answer, no matter what it cost and how ugly your code looks like. Actually, this is the basis of advanced programming courses, where the correctness should always be prioritized.

- In the class of **Data Structure and Algorithms**, we learn how to evaluate the time ans space loss of some code and algorithms. We analyze the code, trying to solve the problem more quickly.

- Now, in today's course: **Architecture Patterns in Python**, we will learn how to write code **in a better manner**. What is called a better manner? Well, it means your code is more:

    - **Performance**: Responsiveness, throughput.

    - **Scalability**: Ability to handle increasing load.

    - **Availability**: Uptime, fault tolerance.

    - **Security**: Protection against unauthorized access and attacks.

    - **Modifiability**/**Maintainability**: Ease of change and evolution.

    - **Usability**: User-friendliness.

    - **Testability**: Ease of testing.

{% note info %}

This series can be seen as the learning notes for the course: **Architecture Patterns with Python**

{% endnote %}

## The zen of Python

```bash
python -c "import this"
```

```text
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

{% note primary %}

Fun Fact: if you dive into source code of `this` module, you will see:

```python
s = """Gur Mra bs Clguba, ol Gvz Crgref

Ornhgvshy vf orggre guna htyl.
Rkcyvpvg vf orggre guna vzcyvpvg.
Fvzcyr vf orggre guna pbzcyrk.
Pbzcyrk vf orggre guna pbzcyvpngrq.
Syng vf orggre guna arfgrq.
Fcnefr vf orggre guna qrafr.
Ernqnovyvgl pbhagf.
Fcrpvny pnfrf nera'g fcrpvny rabhtu gb oernx gur ehyrf.
Nygubhtu cenpgvpnyvgl orngf chevgl.
Reebef fubhyq arire cnff fvyragyl.
Hayrff rkcyvpvgyl fvyraprq.
Va gur snpr bs nzovthvgl, ershfr gur grzcgngvba gb thrff.
Gurer fubhyq or bar-- naq cersrenoyl bayl bar --boivbhf jnl gb qb vg.
Nygubhtu gung jnl znl abg or boivbhf ng svefg hayrff lbh'er Qhgpu.
Abj vf orggre guna arire.
Nygubhtu arire vf bsgra orggre guna *evtug* abj.
Vs gur vzcyrzragngvba vf uneq gb rkcynva, vg'f n onq vqrn.
Vs gur vzcyrzragngvba vf rnfl gb rkcynva, vg znl or n tbbq vqrn.
Anzrfcnprf ner bar ubaxvat terng vqrn -- yrg'f qb zber bs gubfr!"""

d = {}
for c in (65, 97):
    for i in range(26):
        d[chr(i+c)] = chr((i+13) % 26 + c)

print("".join([d.get(c, c) for c in s]))
```

{% endnote %}

## 基本概念

**设计模式**的概念本身是为了解决**现代软件工程开发中**代码量大，难以重构的问题。语言层面在给予开发者灵活性的同时，有不可避免地带来了管理和统一上的复杂性。如此以往便会产生雪崩效应，甚至于产生难以理解，晦涩难懂或者说，**屎山代码**。

```python
# some of the shit code the author have written in college
def process_data(data_matrix):
    i = 0
    while i < len(data_matrix):
        for j in range(len(data_matrix[i])):
            if data_matrix[i][j] % 2 == 0:
                k = 0
                while k < data_matrix[i][j]:
                    if k % 3 == 0 and k != 0:
                        data_matrix[i][j] -= 1
                    else:
                        data_matrix[i][j] += 1
                    k += 1
            elif data_matrix[i][j] % 5 == 0:
                for x in range(data_matrix[i][j] // 5):
                    data_matrix[i][j] = data_matrix[i][j] + x - (data_matrix[i][j] % 2)
            else:
                data_matrix[i][j] *= -1
        i += 1
    return data_matrix

matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
processed_matrix = process_data(matrix)
```

而设计模式就是类似于一本**葵花宝典**，他讲实际软件开发过程中的常见场景进行归纳和抽象，并且配上解决这些场景的代码范式。这有利于开发者在开发过程中形成统一的共识，提升合作开发的效率。

### 封装和抽象

- 封装：**简化行为** & **隐藏数据**

- 设计模式中的“抽象”是指从**具体的实现中分离出概念或本质特征的过程**。它允许你关注事物的“是什么”（what it is），而不是“怎么做”（how it's done）。

    - Python 调用第三方包就是一种高级抽象行为。

> You can see Duck Programming in Python.


### 分层

在复杂代码中，各种各样的变量，函数，对象形成了**复杂的依赖关系**，这也让代码重构变的困难，因为各个组件都深度的耦合在一起，调整接口会引发各种不兼容的冲突。因此，**将业务和代码分层是常见的做法之一**。

### SOLID


SOLID 是面向对象设计（OOD）和编程中，**五个基本原则**的首字母缩写。这些原则旨在使软件设计更易于理解、更灵活、更易于维护和扩展。它们由罗伯特·C·马丁推广，是许多现代软件开发实践的基础。

#### 单一职责原则 (SRP: Single Responsibility Principle)

**核心思想：** 一个类或模块只应该有一个改变的理由。换句话说，一个类应该只负责一项功能或一个责任。


#### 开放封闭原则 (OCP: Open/Closed Principle)

**核心思想：** 软件实体（类、模块、函数等）应该对扩展开放，对修改封闭。这意味着你可以在不修改现有代码的前提下，增加新的功能。

- **举例：** 假设你有一个计算不同形状面积的函数。如果每次增加一种新形状（比如三角形），你都要修改这个函数内部的 `if/else if` 语句来添加新的面积计算逻辑，那就违反了 OCP。

- **OCP 提倡：** 定义一个 `Shape` 接口或抽象基类，其中包含一个抽象的 `calculate_area()` 方法。然后，`Circle`、`Rectangle`、`Triangle` 等具体形状类都实现这个接口或继承这个抽象类，并提供自己的 `calculate_area()` 实现。这样，当你需要增加 `Triangle` 时，只需创建新的 `Triangle` 类，而无需修改原有的面积计算函数。

#### 里氏替换原则 (LSP: Liskov Substitution Principle)

**核心思想：** 子类型必须能够替换掉它们的基类型，并且客户端程序不会因此而受到影响。简而言之，如果 S 是 T 的子类型，那么在任何使用 T 的地方，都可以用 S 来替换，而不会导致程序出错或行为异常。

- **举例：** 考虑一个 `Bird` 类和它的 `fly()` 方法。如果 `Penguin`（企鹅）继承了 `Bird` 类，但是 `Penguin` 的 `fly()` 方法会抛出错误（因为企鹅不会飞），那就违反了 LSP。

- **LSP 提倡：** 设计继承关系时，**要确保子类在行为上与父类兼容** (这也是设计类之间复杂继承关系的依据之一! Is B a kind of A?)。对于企鹅的例子，可以考虑重新设计继承结构，比如引入一个 `FlyingBird` 接口，只有会飞的鸟才实现它；或者 `Bird` 类本身不包含 `fly()` 方法，而是让特定的子类（如 `Sparrow`）去实现。

#### 接口隔离原则 (ISP: Interface Segregation Principle)

**核心思想：** 客户端不应该被强迫依赖于它们不使用的接口。换句话说，大型的、臃肿的接口应该被拆分成更小、更具体的接口，每个客户端只依赖它实际需要的方法。

- **举例：** 假设你有一个 `Worker` 接口，它包含了 `work()` 和 `eat()` 两个方法。如果 `RobotWorker` 实现了这个接口，但机器人不需要“吃”，那么它就被迫依赖了一个它不使用的方法。

- **ISP 提倡：** 将大接口拆分成多个小接口。比如，可以定义 `Workable` 接口（包含 `work()`）和 `Eatable` 接口（包含 `eat()`）。这样，`HumanWorker` 可以同时实现 `Workable` 和 `Eatable`，而 `RobotWorker` 只实现 `Workable` 即可。

#### 依赖倒置原则 (DIP: Dependency Inversion Principle)

**核心思想：**
1.  高层模块不应该依赖低层模块，两者都应该依赖抽象。
2.  抽象不应该依赖于细节，细节应该依赖于抽象。（**在开始写代码前规划项目非常重要**！！！）

- **举例：** 传统上，高层模块（如业务逻辑）直接调用低层模块（如数据库访问代码）。例如，一个 `OrderProcessor` 直接依赖于 `MySQLDatabase` 类。这样，当需要更换数据库时，`OrderProcessor` 也要修改。

- **DIP 提倡：** 引入抽象层（接口或抽象类）。`OrderProcessor` 应该依赖于一个 `IDatabase` 接口，而 `MySQLDatabase` 和 `PostgreSQLDatabase` 等具体数据库实现都去实现 `IDatabase` 接口。这样，高层模块只与抽象交互，解除了与具体实现之间的耦合。

    - 这其实就是一个**统一度量衡**的事情，引入**不会经常改变的第三方抽象接口**来规范原有代码的行为。 

> 计算机科学的所有问题都可以通过**增加另一个间接层次来解决**。


### 其他设计模式概览

在介绍复杂的设计模式之前，**勿忘Python之禅：Simple is better than complex**! 任何设计模式的初衷都是为了简化代码复杂性（注意不是简化代码，有时候代码长度太短也不一定是件好事情）。因此，我们首先从几个简单的设计模式思想开始讲起。

- **TDD**：测试驱动开发

TDD 的核心思想是“**先写测试，再写代码**”。根据需求，先写一个自动化测试用例，这个测试一开始肯定是失败的，因为它所对应的功能还没实现，接着，编写刚好能让这个测试通过的代码。在测试通过后，对代码进行重构，优化结构、消除冗余，同时确保所有测试仍然通过。

这个过程是循环进行的，通过不断地“红（测试失败）-绿（测试通过）-重构”循环，来逐步完善功能。

这也是绝大部分开发者会采用的步骤：**在测试中不断重构并且优化代码**，不过，编写**强有力的测试**也是本课程讨论的重点之一。

- **DDD**：领域驱动设计

DDD 是一种软件开发方法论，它强调将软件设计与核心业务领域（Domain）的概念和逻辑紧密结合。简单来说，就是让开发人员深入理解业务，并用业务的语言来构建软件模型。

> 这个概念相对更加复杂，我们稍后逐步深入。


- **松耦合微服务**：

松耦合微服务 是一种架构风格，它将一个大型的单一应用程序（单体应用）分解成一组小型、独立部署的服务。这些服务之间尽可能地减少相互依赖，每个服务都围绕着特定的业务功能构建，并且可以独立开发、部署和扩展。

例如，现代软件架构中的前后端分离~~人不分离~~，Linux哲学的小而美原则，都和这种微服务的思想有着异曲同工之妙。


## Table of Content (in the future)

Roadmap to be done.
