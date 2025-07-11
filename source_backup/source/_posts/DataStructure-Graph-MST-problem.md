---
title: DataStructure Graph MST problem
date: 2025-05-11 20:22:14
index_img: /img/cover/Kruskal.jpg
excerpt: Minimum Spanning Trees in the Graph, including prim and kruskal algorithms.
math: true
hide: false
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Graph
  - Finished
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Minimum Spanning Trees (最小生成树问题)

## Definition

图的结构相比于树结构更加复杂，很重要的一点原因在于**有向图的父亲节点**（或者说入度）可以多于1个，但是**树需要严格保证为1个**（根节点除外）。我们当然可以把**树**看做一种特殊的**图**的结构，满足性质:$|V| = |E| + 1$。

对于一个连通图，我们可以证明$|V| \le |E| + 1$。因此，最小生成树问题就是**尽可能的在保持连通性的前提下**，使得权重最小。（因为树结构总是可以保证联通的）

> 当然，我们需要保证原图是联通图，并且我们默认为**无向图**。

{% note primary %}

**我们可以从图的角度出发定义树**！

Trees are **connected** **acyclic(no circles)** undirect graphs. 

不可以使用$|V| = |E| + 1$来定义树，因为这是必要的条件。

{% endnote %}

因此，我们需要设计的算法需要**在给定一个无向联通图**的情况下，找到一颗最小生成树。

## Preliminaries

在学习算法之前，我们首先需要介绍一个非常重要的定理，这也是算法正确性的基础。

Assume $G = (V,E)$ is a connected graph, $U$ is a no-empty subset of $V$, if $(u,v)$ is is the edge that has lowest cost and $u \in V$ and $v \in V -U$, there exists a MST, where edge $(u,v)$ is included.

证明：使用**反证法**。由最小生成树的存在性，我们知道存在树$T$，为其最小生成树，并且**不包含**$(u,v)$。

将$(u,v)$加入树中，此时图会出现回路，需要**删除一条边**。或者换句话说，因为$T$联通，因此存在通路从$u$走向$v$。

又因为$U$ 和 $V - U$构成了一个对$V$的划分，因此存在一条边$(u',v')$, where $u' \in U$ and $v' \in V - U$.

Delete that edge, we can create a new tree $T'$, then we can prove $T'$ is a tree and is smaller than $T$. 

## Prim's Algorithm

Basic Ideas: Grow the tree in the successive stages.

![Prim](https://s1.imagehub.cc/images/2025/05/12/ec488d9b4830a3e2852f12ebc2cad9cf.png)

> The image is from: [Shusen Wang's PPT](https://www.youtube.com/watch?v=GLlIaT_PxVE), so great!

具体解释就是：

- 每次更新完之后看**已经被加入树的节点**和**没有被加入树的节点**之间的连接边。即我们需要在集合$E'$中挑选权重最小的一条边：

$$E' = \\{ e \in E|\exists(u\in V',v\notin V')( e \  \text{connects } u,v) \\}$$

- 接着检查是否符合树的定义：
	- 如果符合进入下一轮循环
	- 没有不符合的情况，因为**上面的数学引理**保证了类似最小生成树的存在性。

## Kruskal's Algorithm

![Krustal](https://s1.imagehub.cc/images/2025/05/12/2cf376b2e618db08f993193a795d5347.png)

Prim算法的关键在**根据权重找节点**，而Krustal算法的思想不一样，**不断加入边**。

我们需要使用**并查集**来维护一个**森林**，或者可以看成是由联通关系构成的等价类划分，在一开始，所有节点之间两两都不联通，我们希望在算法结束的时候**这个森林**被Union成一个等价类，即形成联通关系，而且不存在回路。

在预处理过程中，我们需要**维护一个队列**，按照权重大小升序排序，并且不断的尝试把边加入这个树中，（**加边的过程**可以看做是对联通关系的更新，也就是对等价类的合并！）

- 如果这条边的两个endpoint本来就在一个等价类中，那么很遗憾，如果加入这条边我们会引入环路，不加
- 如果不在，加入这条边，并做Union操作。

一直到第一次整棵树全部联通（或者生成的树含有$|V|$个节点），算法结束。

### A small question

最小生成树唯一吗？对于一般的情况，肯定是不唯一的，并且我们证明的数学定理也只是**确保了存在性**而非**唯一性**。

如果我们保证每一条边的权值是不一样的，那么**优先级队列的排列方式**唯一，我们可以得到唯一的生成树。

换句话说，每一种最小生成树，都对应着一种Krustkal算法的生成结果，这是Prim算法所不能做到的。

{% note primary %}

**当且仅当图中所有边的权重互不相同**，或者**在所有具有相同权重的边中，这些边不会在MST的选择过程中产生替代方案**时，MST是唯一的。

{% endnote %}

## Time complexity

对应Kruskal算法：

- 对所有边构建优先级队列排序：$O(E\log E)$
- 加边：$O(V)O(\alpha(V))$，可以看成$O(V)$
- 总的时间复杂度：$O(E \log E)$

对于Prim算法：

- $O(E \log V)$

