---
title: DataStructure Graph Mathematical Basis
date: 2025-05-15 00:34:14
index_img: /img/cover/graphds.jpg
excerpt: Introduction to graph theory algorithms, lecture notes for discrete mathematics.
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
  - Discrete Mathematics
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Math for Graph Theory

## Connect Components

## Adding Edges into Graphs

### Directed Graphs

For Graph $G = (V,E)$, we now add one edge from $u$ to $v$ to make it as $G' = (V', E')$. Now consider:

- If $u$ and $v$ are mutually connected in $G$:
	- Nothing Changes!
	- Or we can say: $\forall w \in V, [w]\_{conn(G)} = [w]\_{conn(G')}$
- If $u$ is not connected to $v$ and $v$ is connected to $u$:
	- The equivalent class of $u$ and $v$ is **unioned**!
	- $[u]\_{conn(G')} = [v]\_{conn(G')} = \\{ w\in V| u \text{ is reachable from } w \text{ and } w \text{ is reachable from } v\\}$
	- 相当于双向可达，即**两个强联通块拼在一起**。不过肯定不止于此，只要满足单向可达就行。
- $u$ is not reachable from $v$.
  - $\forall w \in V, [w]\_{conn(G)} = [w]\_{conn(G')}$
  - **这个证明很经典**

## Trees in Graph

**非空联通没有回路的无向图**。

> 我们这里定义的是**无根树**，和数据结构中的**有根树**存在一定的差别。

- Adding an edge into a tree, there will be a simple circuit.
- Removing an edge, the tree will be unconnected.

### leaf

A vertex is leaf when the degree of this vertex is 1.

- If a tree has at least two vertices, then it will have at least 2 leaf.
- A tree has $n$ vertices has $n-1$ edges.

因此，树在图中具有**非常良好的性质**：

- We define the tree $T = (V, E_T)$ is a subgraph of graph $G = (V, E)$:
	- **We suppose G is a connected and undirected graph**. (Ensure $\exists T$)
	- $E_T \subseteq E$
	- $T$ is the **maximum graph which has no circles** for all the subgraphs in $G$.
	- $T$ is the **minimum connected graph** for all the subgraphs in $G$
	- $T$ may not be the only tree for graph $G$.

