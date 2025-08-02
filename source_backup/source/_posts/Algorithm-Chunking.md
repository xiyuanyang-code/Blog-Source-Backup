---
title: Algorithm-Chunking
date: 2025-03-03 11:36:13
index_img: /img/cover/chunking.jpg
excerpt: This article introduces the chunking algorithms.
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - Chunking
  - tutorial
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
# Algorithms: Chunking

**分块算法**

## Introduction

现在考虑如下问题：

- 对于一个规模较大的数组
- 现在需要频繁地发送**区间请求**，例如：
	- 查询一个区间的数组元素之和
	- 对一个区间的数组进行批量运算（例如同时+-*/一个数）
	- 其他更加复杂的运算

暴力解法是非常容易实现的，但是时间复杂度过高，原因是**进行了很多重复性的运算**。我们需要追求更加高效的算法。在本文中，我们将介绍最基本的**分块算法(Chunking Algorithms)**，为这一类问题提供小于线性时间复杂度的算法。

## Example1: Adding numbers

题目: 现在给定一个数组`arr`，给出以下指令：

- `A l r n`：表示对区间[l,r]的所有元素的值都加上n。
- `Q i`：表示**查询**操作，指查询特定索引(0-based)上的数组值。

对于比较暴力的做法，是对每一次**Add**的操作时，使用for循环遍历更新对应数组的值，最后直接查询输出。复杂度O(mn)，m为操作次数，n为数组长度。

为什么**分块**能够优化这个问题？因为我们观察到**相邻元素在操作时的行为在很大概率上是相同的**，因此暴力的遍历会浪费很多时间。举一个具体的例子：对应`arr[30]`和`arr[31]`，只要我规定的区间**同时包含了这两个元素**，那行为就是完全相同。换句话说，我们可以**把相邻的元素打包在一起**，对于相同的操作**统一处理**，这样就能极大的减少时间复杂度！

这就是**分块**，将一整个区间分成几小块，再对所分的块进行批量处理。（**优化的暴力**）

首先，我们给出**分块**的基本架构：

```cpp
#include <iostream>
#include <cmath>

int main(){
    int total_length = 0;
    std::cin >> total_length;
    int* arr = new int[total_length];

    //chunking!
    int block_size = (int)std::sqrt(total_length);
    int block_count = std::ceil((double)total_length / block_size);
    
    return 0;
}
```

我们将一整个`total_length`的长度**取根号**作为分块的长度（由基本不等式可以证明此时时间复杂度最低）。

接下来，我们需要解决的问题是：**如何处理区间的add操作**？这便是**分块思想以空间换时间**的思想，通过引入`tag`数组，来实现数组的批量加法。具体而言就是：

- 创建一个新数组`add-tag`，长度为`block_count`。用来记录**这一个分块中批量处理的加和值**。
- 在查询的时候，元素的实际值 = 数组中元素记录的值 + `add_tag`中元素所在块对应的值。

> `add-tag`是**分块**的核心，原先需要遍历一整个块的元素实现相同的**重复操作**，但现在只需**额外维护一个数组就可以实现**！
