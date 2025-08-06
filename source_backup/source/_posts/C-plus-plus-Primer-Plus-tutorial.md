---
title: C-plus-plus-Primer-Plus-tutorial
date: 2024-12-04 07:26:42
index_img: /img/cover/welcomecpp.jpg
excerpt: This blog anncounces the updating plan of the Object Oriented Programming Section of C++, based on the classic textbook "C++ Primer Plus"
categories:
  - Code
  - Cpp
tags:
  - Announcement
  - Finished
  - C/C++
  - OOP
  - C++ Primer Plus
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# C++ Primer Plus tutorial

## 缘起

笔者又来开新坑啦！

之前看到一个很讽刺的笑话：**大多数国内高校学生学到的C++只是C语言+cin/cout**。这当然只是一句玩笑话，但很深刻地反映出对于C++精髓**面向对象编程**的忽视。

这是有理可依的：**编程语言的学习本身就和传统的课堂授课模式存在较大的出入**，编程重视实践，枯燥的语法讲解如同天书一般晦涩难懂，更不用提OOP所涉及的都是比较大规模的项目工程，如果只是在课堂上乏味地讲解“什么是析构函数，什么是继承，什么是多态···”，很容易将C++学成死记硬背的无聊学科。

因此，笔者在课堂学习之余，**同步学习了世界经典C++教材——《C++ Primer Plus》的相关内容**。经典不愧为经典！

但是，针对我个人而言，我认为C++ Primer Plus有以下问题：（其实也不算是问题啦，就是和普通在校大学生存在一些“冲突”）

- 一共678页，并且全是密密麻麻的字，阅读周期长，读完需要很大的意志力（对于自学者）。
- 由于翻译等种种的原因，在很多地方的解释并不清晰（作为一本C++的入门书籍体量有些大了，而且涉及到很多专业的名词，看得让人头大）

因此，笔者希望通过博客的方式，记录自己的C++ Primer Plus学习笔记，并上传到网络上供一同学习的小伙伴参考。

博客立志于：

- 记录自己的C++学习过程
- 对本人认为的C++学习中的一些重难点做一些额外的解释
- 尝试用简单清晰的语言梳理逻辑脉络

## 更新内容范围

- **Chapter10 对象和类**
- **Chapter11 使用类**
- **Chapter12 类和动态内存分配**
- **Chapter13 类继承**
- **Chapter14 代码重用**
- **Chapter15 友元，异常和其他**
- **Chapter16 string类的标准模板库**

（目前更新计划主要分布在OOP的章节，之后的其他章节看情况更新~）

## 实际更新的目录

[系列文章](https://xiyuanyang-code.github.io/tags/OOP/)

{% note info %}

### Introduction to OOP

欢迎来到**面向对象编程**的世界！首先文章将会向你介绍在面向对象编程中四个最核心的精神：**封装、继承、多态和抽象**，并对面向对象所涉及的知识做一个总体性的概览。接下来，你将系统学习到什么是**类和对象**，以及如何创建并使用自己的类和对象。文章的内容涵盖**构造函数**的四种定义、**析构函数**的使用、**静态和动态**数据成员以及成员函数、**友元函数**、**const函数**的用法、**this指针**以及类的自动类型转换等知识。

Welcome to the world of **Object-Oriented Programming (OOP)**! This article will first introduce you to the four core principles of OOP: **Encapsulation, Inheritance, Polymorphism, and Abstraction**, and provide an overview of the knowledge involved in OOP. Next, you will systematically learn what **classes and objects** are, and how to create and use your own classes and objects. The article covers topics such as the four definitions of **constructors**, the use of **destructors**, **static** and **dynamic** data members and member functions, **friend functions**, the usage of const functions, the **`this`** pointer, and **automatic type conversion of classes**, among others.

**Key words: OOP, constructors, destructors, this pointer, friend functions**

### Dynamic Memory and Class

本章将重点放在如何对**自定义类和对象**谨慎地使用**动态内存分配**，以及内存管理的相关知识。首先从一个代码示例StringBad讲起，分析因为C++自带的隐式复制构造函数导致**按值传递**和**按址传递**发生冲突而导致程序无法正确输出，并以此为教材介绍了如何**显式**地定义**复制构造函数和赋值运算符**，并给出了修改后的String类的类定义和使用示范。接着，文章聚焦于在类中使用动态内存的易错点，包括new和delete的一一对应问题。最后，文章分析了使用动态内存分配在设计类和对象过程中的应用：**设置合理的返回对象**和**使用指向对象的指针**，同时介绍了动态内存管理的一些高级操作，包括**正确地使用析构函数**和**使用定位new运算符**。

This chapter focuses on how to **cautiously use dynamic memory allocation with** **custom classes and objects**, along with related knowledge on memory management. It begins with a code example, StringBad, to analyze how the implicit copy constructor provided by C++ leads to conflicts between **pass-by-value** and **pass-by-reference**, resulting in incorrect program output. Using this example as a teaching tool, the chapter introduces how to **explicitly** define **copy constructors and assignment operators**, and provides an updated class definition and usage demonstration for the String class. Next, the article focuses on common pitfalls when using dynamic memory in classes, including the one-to-one correspondence issue between new and delete. Finally, the chapter discusses the application of dynamic memory allocation in the design of classes and objects: **setting appropriate return objects** and **using pointers to objects**, while also introducing some advanced operations in dynamic memory management, such as **correctly using destructors** and **using placement new operator**.

**Key words: OOP, Dynamic Memory, C++, Classes**

### Class Inheritance

在本文中，你将学到面向对象编程中的又一个核心思想：**继承与多态**。本文从一个基本的基类讲起并延伸到派生类的定义和基本使用，探讨**派生类和基类的特殊关系**。接下来本文深入继承的原理，介绍在多态公有继承中的**is-a**关系和虚函数，已经动态联编和静态联编的基本知识。接着本文介绍了**抽象基类**的使用以及在派生类中使用**动态内存分配**的注意事项。最后本文对OOP的基本精神——封装、继承与多态和相关基础知识做了梳理。

In this article, you will learn another core concept in object-oriented programming: **inheritance and polymorphism**. The article begins with a basic base class and extends to the definition and basic usage of derived classes, **exploring the special relationship between derived and base classes**. Next, the article delves into the principles of inheritance, introducing the **is-a** relationship in polymorphic public inheritance, virtual functions, and the basics of dynamic and static binding. Then, the article discusses the use of **abstract base classes** to improve efficiency, as well as considerations for using **dynamic memory allocation in derived classes**. Finally, the article summarizes the fundamental principles of OOP—encapsulation, inheritance, and polymorphism—along with related foundational knowledge.

**Key words: OOP, Class Inheritance, Abstract Base Classes, Polymorphism**

### Code Reuse in OOP

C++的一个主要目标是促进代码重用。公有继承是实现这种目标的机制之一，但并不是唯一的机制。本章将介绍其他方法，其中之一是使用这样的类成员：本身是另一个类的对象。这种方法称为**包含（containment）、组合（composition）或层次化（layering）**。另一种方法是使用私有或保护继承。通常，**包含、私有继承和保护继承**用于实现 **has-a** 关系，即新的类将包含另一个类的对象。多重继承使得能够使用两个或更多的基类派生出新的类，将基类的功能组合在一起。同时，本章将介绍**类模板**——另一种重用代码的方法。类模板使我们能够使用通用术语定义类，然后使用模板来创建针对特定类型定义的特殊类。

One of the main goals of C++ is to promote code reuse. **Public inheritance** is one of the mechanisms to achieve this goal, but not the only one. This chapter will introduce other methods, one of which is to use class members that are themselves objects of another class. This method is called **containment**, **composition, or layering**. Another method is to use **private or protected inheritance**. Generally, containment, private inheritance, and protected inheritance are used to implement the **has - a relationship**, that is, the new class will contain an object of another class. Multiple inheritance enables new classes to be derived from two or more base classes, combining the functionality of the base classes.At the same time, this chapter will introduce **class templates** - another method of reusing code. Class templates enable us to define classes in general terms and then use the templates to create special classes defined for specific types.

**Key words: Public Inheritance, Containment, class template, composition**

{% endnote %}

## 声明

本博客系列使用的大部分代码都摘自《C++ Primer Plus》。
