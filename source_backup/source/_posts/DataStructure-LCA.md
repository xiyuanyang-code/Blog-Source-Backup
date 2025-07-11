---
title: DataStructure-LCA
date: 2025-04-10 12:34:06
index_img: /img/cover/lca.jpg
excerpt: Introduction to the LCA(lowest common ancestor) problem, including doubling algorithm and eulerian sequence.
math: true
hide: false
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Tree
  - Finished
  - LCA
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Data Structure: LCA

LCA: （**Lowest Common Ancestor**）**最近公共祖先问题**

## Introduction

什么是**公共祖先问题**？我们进行问题重述：

给定一棵**有根树**$T$，考虑两个节点$u$，$v$，则要求找出树中的一个节点$target=LCA(T,u,v)$，使节点$target$满足：

- $target$是节点$u$和$v$的**公共祖先**。
- 在满足性质1的条件下，要求**$target$的深度尽可能的深（即离根节点尽可能的远，离两个节点尽可能的近）**。

对于该问题，我们需要对一棵节点数为$N$的树进行$M$次询问。

> LCA有什么具体的性质？例如，我希望给定两个节点，**求这两个节点的树的最短路的问题**，我们可以很容易证明这条最短路一定会经过LCA。

## 朴素解法

解决单次询问并不是很难的一件事，我们可以在$O(h)\le O(N)$的时间复杂度下完成。

首先，我们需要移动两个节点到**同一深度**，即$height$相同。接下来需要两个节点不断完成**找爸爸**的操作，**找到第一个相遇的点即为所求**。

显然，对于最坏的情况，（比如一棵严重退化的树），完成$M$次查询的复杂度是$O(MN)$。

## 倍增优化

如何优化？我们先把两个节点调整到相同的高度。由于我们计算的是**最近**公共祖先问题，我们显然知道：**如果$S$是最近公共祖先，那么$S$的所有祖先节点**肯定也都是满足条件1的公共祖先！因此，如果把**是不是一个公共祖先**定义为一个bool值，那么这明显是一个单调递减的序列（$111..10000..00$）,我们可以使用**二分查找**的方式来找到最终的答案，二分查找本身的复杂度是$O(\log n)$。

但是这样存在一个问题，**我们需要在低于线性时间复杂度内知道每一个节点向上跳n次会调到哪一个节点**，即$upd(u,d)$，$u$是节点，$d$是向上跳的层数，最终返回一个节点值。我们需要进行预处理，来实现这个函数的高效运算。

如何预处理？**倍增法！**（因为这不是一个可重复性贡献问题），我们可以使用$fa[u][i]$代表$upd(u,d=2^i)$的值，然后使用**倍增法**拼起来，这样就可以实现每次查询$O(\log n)$的时间复杂度。对于预处理的过程，可以直接使用**深度优先搜索**，在预处理好祖先节点的时候，使用**动态规划**就计算当前节点的预处理的值，并且继续递归处理当前节点的子节点。

总结一下，倍增优化的优化之处：

{% note primary %}

- 预处理：预处理使**向上跳很多次**的过程在查询过程中达到$O(1)$的复杂度。
- 之后可以实现跳很多步，相当于二进制的拆解过程。

{% endnote %}

代码如下：

```cpp
// Using binary lifting to solve LCA problem with a tree structure
#include <cstring>
#include <iostream>

const int MAXN = 1005;
const int LOG = 20;

int tree[MAXN][MAXN]; // Tree structure using adjacency matrix
int child_count[MAXN];// Number of children for each node
int depth[MAXN];      // Depth of each node
int up[MAXN][LOG];    // up[i][j] represents the 2^j-th ancestor of node i

// Add an edge to the tree
void add_edge(int u, int v) {
    tree[u][child_count[u]++] = v;// Add v as a child of u
    tree[v][child_count[v]++] = u;// Add u as a child of v (undirected tree)
}

// DFS preprocessing to fill the `up` and `depth` arrays
void dfs(int u, int parent) {
    up[u][0] = parent;// Direct parent node
    for (int i = 1; i < LOG; ++i) {
        up[u][i] = up[up[u][i - 1]][i - 1];// Binary lifting
    }

    // Traverse all children of the current node
    for (int i = 0; i < child_count[u]; ++i) {
        int v = tree[u][i];
        if (v != parent) {
            depth[v] = depth[u] + 1;
            dfs(v, u);
        }
    }
}

// Compute the LCA of two nodes
int lca(int u, int v) {
    // Ensure u is the deeper node
    if (depth[u] < depth[v]) {
        std::swap(u, v);
    }

    // Lift u to the same depth as v
    for (int i = LOG - 1; i >= 0; --i) {
        if (depth[u] - (1 << i) >= depth[v]) {
            u = up[u][i];
        }
    }

    // If u and v are the same, return the result
    if (u == v) {
        return u;
    }

    // Lift u and v simultaneously until their parents are the same
    for (int i = LOG - 1; i >= 0; --i) {
        if (up[u][i] != up[v][i]) {
            u = up[u][i];
            v = up[v][i];
        }
    }

    // Return the LCA
    return up[u][0];
}

int main() {
    memset(tree, 0, sizeof(tree));              // Initialize the tree
    memset(child_count, 0, sizeof(child_count));// Initialize child counts

    int n = 9;

    // Manually construct the tree
    add_edge(1, 2);
    add_edge(1, 3);
    add_edge(2, 4);
    add_edge(2, 5);
    add_edge(3, 6);
    add_edge(3, 7);
    add_edge(6, 8);
    add_edge(6, 9);

    // Preprocessing
    depth[1] = 0;// Assume the root node is 1
    dfs(1, 1);   // The parent of the root node is itself

    // Manually query LCA
    std::cout << "LCA(4, 5) = " << lca(4, 5) << std::endl;// Expected: 2
    std::cout << "LCA(4, 6) = " << lca(4, 6) << std::endl;// Expected: 1
    std::cout << "LCA(3, 4) = " << lca(3, 4) << std::endl;// Expected: 1
    std::cout << "LCA(8, 9) = " << lca(8, 9) << std::endl;// Expected: 6
    std::cout << "LCA(5, 9) = " << lca(5, 9) << std::endl;// Expected: 1

    return 0;
}
```

使用倍增法，我们可以实现在预处理过后**单次查询的复杂度是$O(\log ^2 (n))$**,同时，既然我们已经使用了倍增算法，就不必再二分查找了，我们可以从大到小枚举$2^i$，如果不一样就直接跳，一样就不要动，这样也可以在$O(\log n)$的时间内完成查询操作。

**最终我们实现算法的复杂度如下**：

- 预处理查询：$O(n\log n)$
- $M$次查询：$O(M\log n)$

## 欧拉序

在介绍欧拉序之前先介绍一下**DFS**序：

**树的DFS序列，也就是树的深搜序，它的概念是：树的每一个节点在深度优先遍历中进出栈的时间序列。**

具体来说，在DFS序列中，每一个节点会出现两次，分别代表**入队时刻**和**出队时刻**，即**开始探索这个节点**和**探索完毕离开这个节点（递归：探索完毕这个节点的所有子节点）**。例如我们可以有如下的代码：

```cpp
/*
implementation of DFS search
*/

#include <iostream>
#include <vector>

class Tree {
private:
    std::vector<std::vector<int>> adjList; // Adjacency list representation of the tree
    std::vector<int> dfsOrder;            // Stores the DFS order

    // Helper function for DFS traversal
    void dfs(int node, int parent) {
        dfsOrder.push_back(node); // Add the current node to the DFS order (entering the node)

        // Visit all children of the current node
        for (int child : adjList[node]) {
            if (child != parent) { // Avoid revisiting the parent node
                dfs(child, node);
            }
        }

        dfsOrder.push_back(node); // Add the current node to the DFS order again (leaving the node)
    }

public:
    // Constructor to initialize the tree with n nodes
    Tree(int n) {
        adjList.resize(n + 1); // Nodes are 1-indexed
    }

    // Add an edge to the tree
    void addEdge(int u, int v) {
        adjList[u].push_back(v);
        adjList[v].push_back(u); // Since it's an undirected tree
    }

    // Perform DFS and return the DFS order
    std::vector<int> getDFSOrder(int root) {
        dfsOrder.clear(); // Clear any previous DFS order
        dfs(root, -1);    // Start DFS from the root node
        return dfsOrder;
    }
};

int main() {
    // Create a tree with 7 nodes
    Tree tree(7);

    // Add edges to the tree
    tree.addEdge(1, 2);
    tree.addEdge(1, 3);
    tree.addEdge(2, 4);
    tree.addEdge(2, 5);
    tree.addEdge(3, 6);
    tree.addEdge(3, 7);

    // Get the DFS order starting from node 1
    std::cout << "DFS Order (with enter and leave) starting from node 1:" << std::endl;
    std::vector<int> dfsOrder = tree.getDFSOrder(1);

    // Print the DFS order
    for (int node : dfsOrder) {
        std::cout << node << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

```
DFS Order (with enter and leave) starting from node 1:
1 2 4 4 5 5 2 3 6 6 7 7 3 1 
```

这棵树长这样：

{% mermaid %}

graph TD
    1[1] --> 2[2]
    1[1] --> 3[3]
    2[2] --> 4[4]
    2[2] --> 5[5]
    3[3] --> 6[6]
    3[3] --> 7[7]

{% endmermaid %}

我们来观察这个序列，会发现重要的性质：选择DFS序中那个$u$开始，$u$结束的子序列中长度一定是偶数，并且所有涉及的节点**全部是u的子孙节点**。换句话说DFS序的最大作用就是把复杂的**树状结构**转化成方便处理的**线性结构**。

在介绍完DFS序列之后，我们就可以介绍欧拉序列了，**欧拉序的定义是：从根节点出发到回到根节点为止，按深度优先遍历的顺序所经过的所有点的顺序。**欧拉序在顺序上反映了**在遍历一棵树的时候向下递归和回溯的具体路径。**

例如下面的树：

```cpp
    // Add edges to the tree
    tree.addEdge(1, 2);
    tree.addEdge(1, 3);
    tree.addEdge(2, 4);
    tree.addEdge(2, 5);
    tree.addEdge(3, 6);
    tree.addEdge(3, 7);
    tree.addEdge(4, 8);
    tree.addEdge(6, 9);
    tree.addEdge(6, 10);
```

其DFS序和欧拉序的顺序如下：

```
DFS Order (with enter and leave) starting from node 1:
1 2 4 8 8 4 5 5 2 3 6 9 9 10 10 6 7 7 3 1 
Euler Order starting from node 1:
1 2 4 8 4 2 5 2 1 3 6 9 6 10 6 3 7 3 1 
```

> 欧拉序的长度为$2n-1$，而DFS序的长度为$2n$，因为**每个点在欧拉序中出现的次数等于这个点的度数（该节点的子孙节点数量）**，因为DFS到的时候加进一次，回去的时候也加进。

观察欧拉序，我们很容易发现：**给定两个节点$u$，$v$，从$u$到$v$的欧拉序路径上一定会经过最近公共祖先，并且只会经过一次！（而且其他的公共祖先都不会出现）**。我们巧妙的利用DFS的性质实现从树结构到线性结构的转化。也就是说，我们只需要求一段区间中**深度最小**的点就是LCA!

> 这样，通过一次欧拉序变化，**我们将求LCA问题**转化为**经典RMQ问题**！
>
> 对于RMQ问题，可以使用ST表等内容解决。（因为是可重复性贡献问题）

代码如下：

```cpp
// Another implementation of the LCA problem using Euler Tour and Sparse Table
#include <cmath>
#include <iostream>
#include <vector>

using namespace std;

class EulerTourLCA {
private:
    vector<vector<int>> adj;// Adjacency list representation of the tree
    vector<int> euler;      // Euler tour order
    vector<int> depth;      // Depth of each node
    vector<int> firstOccur; // First occurrence of each node in the Euler tour
    vector<vector<int>> st; // Sparse table (stores indices)
    int n;                  // Number of nodes

    // Recursive function to generate Euler tour, depth, and first occurrence
    void dfs(int u, int parent, int currentDepth) {
        firstOccur[u] = euler.size();// Record the first occurrence of the node
        euler.push_back(u);          // Add the current node to the Euler tour
        depth[u] = currentDepth;     // Record the depth of the node

        for (int v : adj[u]) {
            if (v != parent) {
                dfs(v, u, currentDepth + 1);
                euler.push_back(u);// Add the current node again when backtracking
            }
        }
    }

    // Build the sparse table (preprocess for range minimum query)
    void buildSparseTable() {
        int m = euler.size();
        int logM = log2(m) + 1;
        st.resize(m, vector<int>(logM));

        // Initialize the first column (interval length = 1)
        for (int i = 0; i < m; ++i) {
            st[i][0] = i;
        }

        // Fill the sparse table using dynamic programming
        for (int j = 1; (1 << j) <= m; ++j) {
            for (int i = 0; i + (1 << j) - 1 < m; ++i) {
                int mid = i + (1 << (j - 1));
                if (depth[euler[st[i][j - 1]]] < depth[euler[st[mid][j - 1]]]) {
                    st[i][j] = st[i][j - 1];
                } else {
                    st[i][j] = st[mid][j - 1];
                }
            }
        }
    }

    // Query the index of the node with the minimum depth in the range [l, r]
    int queryMinIndex(int l, int r) {
        if (l > r) swap(l, r);
        int k = log2(r - l + 1);
        int mid = r - (1 << k) + 1;

        if (depth[euler[st[l][k]]] < depth[euler[st[mid][k]]]) {
            return st[l][k];
        } else {
            return st[mid][k];
        }
    }

public:
    EulerTourLCA(int numNodes) : n(numNodes), adj(numNodes), firstOccur(numNodes), depth(numNodes) {}

    void addEdge(int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    void preprocess() {
        dfs(0, -1, 0);// Assume the root node is 0
        buildSparseTable();
    }

    int findLCA(int u, int v) {
        int l = firstOccur[u];
        int r = firstOccur[v];
        int index = queryMinIndex(l, r);
        return euler[index];
    }
};

int main() {
    int n = 10;
    EulerTourLCA solver(n);

    // Build the tree (same structure as the original problem)
    solver.addEdge(0, 1);
    solver.addEdge(0, 2);
    solver.addEdge(1, 3);
    solver.addEdge(1, 4);
    solver.addEdge(2, 5);
    solver.addEdge(2, 6);
    solver.addEdge(5, 7);
    solver.addEdge(5, 8);
    solver.addEdge(7, 9);

    solver.preprocess();// Preprocess the tree

    // Validate test cases
    cout << "LCA(3,4) = " << solver.findLCA(3, 4) << endl;// Should output 1
    cout << "LCA(3,9) = " << solver.findLCA(3, 9) << endl;// Should output 0
    cout << "LCA(8,9) = " << solver.findLCA(8, 9) << endl;// Should output 5
    cout << "LCA(7,0) = " << solver.findLCA(7, 0) << endl;// Should output 0

    return 0;
}
```

**最终我们实现的算法复杂度如下**：

- 预处理求欧拉序数组：$O(n)$
- 预处理求ST表：$O(n\log n)$
- M次查询，每一次都是$O(1)$的常数时间复杂度。

因此整体时间复杂度为$O(n\log n+M)$。

> RMQ问题也可以转化成**LCA**问题来解决，将原序列转化为对应的**笛卡尔树**即可。 

## 并查集 (Tarjan离线算法)

**后序遍历 & 不相交集**

我们需要对这棵树进行**后序遍历**，我们假设在某个时刻已经被后续遍历的元素所构成的集合为$K \subseteq S$。

- 把每一颗子树看做一个等价类，子树的根是等价类的标志
- 后续遍历这棵树，计算每一个节点的等价类（**当前节点为根节点的子树作为一个集合**）
	- 相当于归并其所有儿子的等价类及其自身

- 此时$K$的元素被更新（有一个新的元素被加入$K$中），并且$K$的等价类也因为`union`操作被更新。
- 对两个目标元素做find，如果都找到了并且属于相同的等价类中，则找到LCA

这里使用图而非树结构，对应的**后序遍历**要被替换成DFS。

```cpp
#include <cstddef>
#include <iostream>
#include <unordered_map>
#include <vector>

using namespace std;

// Disjoint Set (Union-Find) data structure
class disjointSet {
private:
    size_t size;
    int *parent;

public:
    disjointSet(size_t s) : size(s) {
        parent = new int[size];
        for (int i = 0; i < size; ++i) {
            parent[i] = -1;
        }
    }

    ~disjointSet() {
        delete[] parent;
    }

    void Union(int root1, int root2) {
        if (root1 == root2) return;

        if (parent[root1] > parent[root2]) {
            parent[root2] += parent[root1];
            parent[root1] = root2;
        } else {
            parent[root1] += parent[root2];
            parent[root2] = root1;
        }
    }

    int Find(int x) {
        if (parent[x] < 0) return x;
        return parent[x] = Find(parent[x]);
    }
};

// Tarjan's offline LCA algorithm implementation
class TarjanLCA {
private:
    vector<vector<int>> adj;
    vector<vector<pair<int, int>>> queries;
    unordered_map<int, int> lcaResult;
    vector<bool> visited;
    vector<int> ancestor; // Maps each node to its current ancestor in the tree

    void dfs(int node, int parent_node, disjointSet &ds) {
        visited[node] = true;

        // Traverse all children
        for (int neighbor : adj[node]) {
            if (neighbor == parent_node) continue;
            dfs(neighbor, node, ds);
            // Merge child's set into current node's set
            int root_node = ds.Find(node);
            int root_neighbor = ds.Find(neighbor);
            ds.Union(root_node, root_neighbor);
            // Update ancestor of the new root to current node
            int new_root = ds.Find(root_node);
            ancestor[new_root] = node;
        }

        // Process all queries associated with this node
        for (auto &q : queries[node]) {
            int v = q.first;
            int idx = q.second;

            if (visited[v]) {
                int root = ds.Find(v);          // Get the representative of v's set
                lcaResult[idx] = ancestor[root]; // Use the stored ancestor
            }
        }
    }

public:
    TarjanLCA(int n) : adj(n), visited(n, false), queries(n), ancestor(n) {
        for (int i = 0; i < n; ++i) {
            ancestor[i] = i; // Initialize each node's ancestor to itself
        }
    }

    void addEdge(int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }

    void addQuery(int u, int v, int idx) {
        queries[u].push_back({v, idx});
        queries[v].push_back({u, idx});
    }

    void computeLCA(int root, int queryCount) {
        disjointSet ds(adj.size());
        dfs(root, -1, ds);

        cout << "\nComputed LCA results:\n";
        for (int i = 0; i < queryCount; ++i) {
            if (lcaResult.find(i) != lcaResult.end()) {
                cout << "LCA of query[" << i << "] is " << lcaResult[i] << endl;
            } else {
                cout << "LCA of query[" << i << "] is not found!" << endl;
            }
        }
    }
};

int main() {
    int n = 10;
    TarjanLCA solver(n);

    // Build the tree
    solver.addEdge(0, 1);
    solver.addEdge(0, 2);
    solver.addEdge(1, 3);
    solver.addEdge(1, 4);
    solver.addEdge(2, 5);
    solver.addEdge(2, 6);
    solver.addEdge(5, 7);
    solver.addEdge(5, 8);
    solver.addEdge(7, 9);

    // Add some LCA queries
    solver.addQuery(3, 4, 0); // Expected: 1
    solver.addQuery(3, 9, 1); // Expected: 0
    solver.addQuery(8, 9, 2); // Expected: 5
    solver.addQuery(7, 0, 3); // Expected: 0

    // Run Tarjan's algorithm from root node (0)
    solver.computeLCA(0, 4);

    return 0;
}
```

## Applications：树上差分

差分数组的**核心用处**：把**区间操作**变为**单点操作**

因此，维护差分数
