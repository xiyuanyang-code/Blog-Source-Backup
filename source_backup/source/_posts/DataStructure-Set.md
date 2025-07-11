---
title: DataStructure-Set
date: 2025-03-24 00:21:54
index_img: /img/cover/set.jpg
excerpt:  Lecture notes for MIT6.006 Lec8 Set and Sorting.
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Set
  - Sorting
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Data Structure: Set

Lecture notes for MIT6.006 Lec8 **Set and Sorting**.

In this Lecture, we will implement a simple interface, **set**.

## Set Interface

Remember Data Structure is an **interface**, don't do some dumb jobs... All you need to do is understanding the first principle of several data structures through practicing it.

![Set interface](https://s1.imagehub.cc/images/2025/03/29/564afb3d8029f1273d4ee1f1ff07b471.png)

If you use simply an array, it will cost $O(n)$ time！

# Data Structure Set 

Lecture Notes from SJTU DS

## Introduction

- 散列表：实现**映射**（集合提升性能的核心问题）

## 集合的定义

集合包含两个部分：**键值**和**关键字值**。

## 静态查找表

问题的定义：给定一个集合，需要查找关键字$X$对应的元素是否存在。

从最基本的顺序查找（$O(n)$）到对于有序集合的**二分查找**($O(\log n)$），还可以进一步优化为**插值查找**，对于数据均匀分布的情况，可以进一步优化时间复杂度：

$$pos = low + \left\lfloor \frac{(high - low - 1) \cdot (target - arr[low])}{arr[high] - arr[low]} \right\rfloor$$

使用**分块查找**也可以进一步优化（详见分块思想）

- 为了最大化发挥分块的效率，我们需要对块内元素的性质做出规定：
	- 例如，第$i$块的元素一定大于第$i+ 1$块的元素（并且建立索引表）
		- 每个索引包括块内元素的最大值和该索引块的起始位置
		- 这样就可以在多次查询的时候减少很多无效的比较，但是**代价是需要额外空间储存索引并且在修改数组时需要更新维护这个索引表**。
	- 如果查找表很大，内存放不下，可以使用分块的思想高效的实现分块查找。



### STL 中的静态查找表

- `find()`：
	- **模版参数**
		- 迭代器类型
		- 集合元素的类型
	- **形式参数**
		- 两个迭代器对象（左闭右开）
		- 需要查找的对象值
	- 返回一个迭代器

- `binary_search()`：使用二分查找的方式查找一个有序序列，返回是否存在





