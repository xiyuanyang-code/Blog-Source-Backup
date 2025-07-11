---
title: Algorithm-BFS-DFS
date: 2025-03-27 20:33:56
index_img: /img/cover/BFSDFS.jpg
excerpt: This blog introduces the basic mathematical definitions of graph theory and discusses the BFS and DFS search algorithms in graph theory, serving as course notes for MIT6.006.
math: true
categories:
  - Algorithm
  - MIT 6.006 Introduction to Algorithms
tags:
  - tutorial
  - Tree
  - Finished
  - BFS/DFS
  - Data Structure
  - Algorithm
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# BFS & DFS

## Terminology

### Graph

![Graph](https://s1.imagehub.cc/images/2025/04/09/41374e84db4a0b25fe40323baf8b9a3f.png)

- We first give the definitions of **graphs**! $G(V,E)$ is a set of vertices $V$ and a set of **Binary Relations**: $E \subseteq V \times V$.

- We don't consider graphs **outside the simple graph**, like edges of $(u,u)$.
- We define $\left | E \right |$ as the numbers of edges in the set $E$. Obviously, $\left |  E\right | \le C_{\left | V \right |}^{2} = O(\left | V\right |^2 )$ for undirected, and $\left |  E\right | \le 2\times C_{\left | V \right |}^{2} = O(\left | V\right |^2 )$ for directed.

### Neighbor Sets/Adjacencies

- The outgoing neighbor set of u ∈ V is $Adj^+(u) = \{v ∈ V | (u, v) ∈ E\}$
- The incoming neighbor set of u ∈ V is $Adj^−(u) = \{v ∈ V | (v, u) ∈ E\}$
-  The out-degree of a vertex u ∈ V is $deg^+(u) = |Adj^+(u)|$
- The in-degree of a vertex u ∈ V is $deg^−(u) = |Adj^−(u)|$
- For undirected graphs, $Adj^−(u) = Adj^+(u)$ and $deg^−(u) = deg^+(u)$
- Dropping superscript defaults to outgoing, i.e., $Adj(u) = Adj^+(u)$ and $deg(u) = deg^+(u)$

### Adjacency list

 A **Set** data structure to map $u$ to $Adj(u)$. Like every node in the tree needs to store the parent and child node, every elements need to store **all its neighbors**!

- Common to use **direct access array or hash table** for $Adj$, since want lookup fast by vertex.
- Common to use **array or linked list** for each $Adj(u)$ since usually only iteration is needed.

### Paths

- A path is a sequence of vertices $p = (v_1, v_2, . . . , v_k)$ where $(v_i, v_{i+1}) ∈ E, \ \forall 1 ≤ i < k$.
- A path is simple if it does not **repeat vertices**.
- The length ($p$) of a path p is the number of edges in the path
- The distance $δ(u, v)$ from $u ∈ V$ to $v ∈ V$ is the **minimum length of any path from $u$ to $v$**, i.e., the length of a shortest path from $u$ to $v$. (by convention, $δ(u, v) = ∞ $ if $u$ is not connected to $v$)

### Model Graph Problems

![Model Graph Problems](https://s1.imagehub.cc/images/2025/04/09/9ae6883f7fe45a85bfba103b58b92af6.png)

We can construct a **shortest path tree** based on the graph with $O(\left |V \right |)$ size. To explain it more clearly, given $a \in V$, then the **shortest path tree** of $a$ is a tree whose root node is $a$ and contains all elements that are reachable to $a$ in the graph. Moreover, the way from the root node $a$ to the node $b$ is **just the shortest path** from $a$ to $b$ in the graph.

## Breadth First Search

How to return a shortest path from **source vertex** $s$ for every vertex in graph? We can define $P(x)$ as the **second to last vertex** on a shortest path from $s$, or you can just imagine it as the parent of $x$ in the constructed shortest path tree of source vertex $s$.

- Define $P(s) == null$.
- Set of parents comprise **a shortest paths tree** with $O(|V|)$ size!
	- Math principles: Assume vertex $u$ appears in the shortest path from the source vertex $s$ to the vertex $x$, which is more remote. **Then the shortest of vertex $u$** is just a subset of this path!
- In the BFS algorithm, we need to compute $δ(s, v)$ and $P(v)$ for all $v \in V$.

 Compute level sets $L_i = \{v | v ∈ V \ \text{and} \ d(s, v) = i\}$ (i.e., all vertices at distance $i$)

![BFS](https://s1.imagehub.cc/images/2025/04/09/9b7a93a15a48fd8854a508068bf94504.png)

The key to BFS is to categorize **vertices by levels**. For vertices at the same level, continuously traverse until all are visited, then proceed to the next level. Obviously, we know the shortest distance from $s$ to $s$ is 0. Then we can **grow** the tree based on it and find the shortest path for all vertex in the graph.

> In real world simulations, we can use **queue** to store different levels of vertices, which means **no vertices in $L_i$ is touched until all vertices in $L_{i-1}$ is traversed**.

**Time complexity analysis**: $O(|V| + |E|)$. (Linear Time!)

## Depth First Search 

### Single source reachability 

- Graph Path Problems
- Single Pair Reachability(G,s,t)
- **Single Source Reachability(G,s)**
- Single Pair Shortest Path(G,s,t)
- Single Source Shortest Paths(G,s) (SSSP)

If we don't need to solve the **SSSP** problem, we can use another algorithm! **Intuition: Visit outgoing adjacencies recursively, but never revisit a vertex**.

> Of course, we can use the **shortest path tree to solve a harder problem (SSSP)**, but it takes linear time! We can actually take less time by using the BFS algorithms.

### Algorithms

For DFS algorithms, we use **recursion** to reach to the deepest depth and then traceback to the previous height.

Before finishing the algorithms, we define $P(u)=v$, there exists a path from $u$ to $v$.

![DFS algorithms](https://s1.imagehub.cc/images/2025/04/12/05369681ede643f4fc83edb46d32e7f7.png)

There maybe some problems! For example, if there exists $(u_1, v)$ and $(u_2, v)$ at the same time, definitely we can say $P(v) = u_1$ and $P(v) = u_2$. But what $P(v)$ actually is the last vertex that is assigned to $P(v)$. Then can this algorithm correctly finds all the reachable vertexes?

Let's do the proof! Claim: **DFS visits $v$ and correctly sets $P(v)$ for every vertex $v$ reachable from $s$**.

We use **induction**, induct on $k$, where $k$ means vertices that can be reachable from source $s$ with the $k$ steps.

- $k = 0$ : is just the source vertex $s$!
- Inductive step: Consider vertex v with $δ(s, v) = k' + 1$. (The minimum length of the two vertexes)
- Consider vertex $u$, **the second to last vertex** on some shortest path from $s$ to $v$.
- By induction, since $δ(s, u) = k'$, DFS visits $u$ and sets $P (u)$ correctly.
- While visiting u, DFS considers $v ∈ Adj(u)$.
- Either $v$ is in $P$ , so has already been visited, or $v$ will be visited while visiting $u$.
- In either case, v will be visited by DFS and will be added correctly to $P$.

Depth First Search: $O(|E|)$. (The number of edges)

### Connectivity of undirected graphs: Full DFS

Obviously, use DFS in one single vertex $s$ cannot reach all vertex in the node. Thus, if we want to reach all vertex in the graph (Graph Traverse), we can actually use **Full-DFS** algorithms.

Iterate for all vertexes in the loop:

- Choose an **arbitrary unvisited vertex $s$**, use **BFS/DFS** to explore all vertices reachable from $s$.
- If the vertex $s$ is reached, then we don't need to use DFS($s$) (let s to be the source node) again!
- Time complexity: $O(|V|+|E|)$.

Full DFS can be used to detect how many **connected components (for undirected graphs) or strongly connected components (for directed graphs)** exist in a graph. Each time a new DFS is initiated, it indicates the discovery of a new connected component.

![Graph Connectivity](https://s1.imagehub.cc/images/2025/04/12/77846aff8face45b5acbf56324eb63d5.png)

### Topological Sort

#### Several Definitions

- **DAGs (Directed Acyclic Graph)**: a directed graph that contains cycles.
	- Of course, a Binary Tree is a DAG😂.
- A **Topological Order** of a graph $G = (V, E)$ is an ordering $f$ on the vertices such that: every edge $(u, v) ∈ E$ satisfies $f(u) < f(v)$.
- A topological order only appears in the DAGs, or it will cause problems! (**iff**)
	- Assume $(u,v)\in E$ and $(v,u) \in E$, which means $f(u) < f(v)$ and $f(v) < f(u)$. Contradiction!
- **A Finishing Order** is the order in which **a Full-DFS finishes visiting each vertex in G**.

#### Claim

The reverse of finishing order is just the **topological order** of the graph.

![Proof](https://s1.imagehub.cc/images/2025/04/12/c95d929f8fd570a477d4573bd2303797.png)

 **Basic principle**: We need to prove the ending of function $visit(u)$ is after the ending of the function $visit(v)$. In Full DFS, every vertex will only be visited once, and the existence of $(u,v)$ enables that:

In the first exploration of $u$, for the recursive function, the ending of function $visit(v)$ is before $visit(u)$. ($u$ is in the lower stack frame.) (Unless that $v$ has been explored previously, which is obviously right).

### Cycle detection

How can we find a graph is **Acyclic**?

Consider the full-DFS algorithms, if **reverse finishing order for Full-DFS is not a topological order, then G must contain a cycle**. Check if G is **acyclic: for each edge (u, v), check if v is before u in reverse finishing order**.

**Claim**: If $G$ contains a cycle, Full-DFS will **traverse an edge** from $v$ to an ancestor of $v$.

To return such a cycle, maintain the set of ancestors along the path back to s in Full-DFS. (Or just the stack frame for the recursion function!)

# Lecture Notes for CS1952 SJTU

## DFS的实现和应用

- DFS需要搜索到所有的节点需要**保证图是联通的**。（实际上这是**充分必要条件**）
- DFS可以使用递归实现，也可以手动使用**栈**来实现。（本质上是一样的）
- BFS使用**队列实现**。

## Applications

### 无向图的连通性

使用BFS或者DFS搜索如果只会生成一棵树，说明**这棵树是联通的**。当然，也可以判断这个图的**联通分量**。

### 欧拉回路

- 奇点个数为$n$, if $n > 2$, then fail
- $n =2$: pass
- $n = 0$: pass

不存在$n = 1$的情况，因为根据**握手定理**：在任何无向图中，所有顶点的度数之和等于图中边数的两倍。

即，我们需要对一张图找到一条路径，使其：

- 起点和终点重合
- 遍历了图中的每一个点一次
- 不存在**重复路径**

如何检查欧拉回路的存在性？**可以使用深度优先搜索**。

![BFS for euler](https://s1.imagehub.cc/images/2025/05/15/ef38b76390e1f20acaf4f7fc7f0b1154.png)

{% note primary %}

换句话说，使用DFS找欧拉回路的过程相当于**单次DFS搜索**的一部分。在一般的DFS搜索中，递归到达最大深度会向上回溯，而欧拉回路**不需要向上回溯**的过程（因为他需要保证搜索相邻节点一定是有边直接相连的）

而是开启**下一个DFS过程**。（**找出路径上另外一个尚有未被访问的边的节点**）

接着，如果成功了，即找到了一个**子环路**，把这个子环路插入到上一级的环路中即可。

{% endnote %}

#### Code

```cpp
#include <climits>
#include <iostream>
#include <string>
#include <vector>

class exception {
protected:
    const std::string variant = "";
    std::string detail = "";

public:
    exception() {
    }
    exception(const exception &ec) : variant(ec.variant), detail(ec.detail) {
    }
    virtual std::string what() {
        return variant + " " + detail;
    }
};

class index_out_of_bound : public exception {
    /* __________________________ */
};

class runtime_error : public exception {
    /* __________________________ */
};

class invalid_iterator : public exception {
    /* __________________________ */
};

class container_is_empty : public exception {
    /* __________________________ */
};

template<class Ver, class Edge>
class graph {
public:
    virtual void insert(Ver x, Ver y, Edge weight) = 0;
    virtual void remove(Ver x, Ver y) = 0;
    virtual bool exist(Ver x, Ver y) const = 0;
};


template<class Ver, class Edge>
class adjListGrapgh : public graph<Ver, Edge> {
private:
    struct edge_node {
        // node for edges

        // index for the second endpoint
        int end;
        Edge weight;
        edge_node *next;

        edge_node(int e, Edge weight_, edge_node *next_ = nullptr) : end(e), weight(weight_), next(next_) {}
    };

    struct ver_node {
        // node for vertices
        Ver value;
        edge_node *head;
        ver_node(Ver value_ = Ver(), edge_node *head_ = nullptr) {
            value = value_;// FIX: assign value
            head = head_;
        }
    };

    struct Eulerian_Node {
        int Node_num;
        Eulerian_Node *next;
        Eulerian_Node(int ver) {
            Node_num = ver;
            next = nullptr;
        }
    };

    ver_node *ver_array;
    int num_vers;
    int num_edges;

private:
    int find(Ver v) const {
        for (int i = 0; i < num_vers; i++) {
            if (ver_array[i].value == v) {
                return i;
            }
        }
        throw invalid_iterator();
    }

    void EulerCircuit(int start, Eulerian_Node *&beg, Eulerian_Node *&end) {
        // initialize
        beg = end = new Eulerian_Node(start);
        while (ver_array[start].head != nullptr) {
            int next_node = ver_array[start].head->end;

            // !attention, it is the undirected graph
            remove(start, next_node);
            remove(next_node, start);
            start = next_node;
            end->next = new Eulerian_Node(start);
            end = end->next;
        }
    }

public:
    /**
     * @brief Construct a new adj List Grapgh object
     * 
     * @param size 
     * @param d 
     */
    adjListGrapgh(int size, const Ver *d) {
        num_vers = size;
        num_edges = 0;

        ver_array = new ver_node[num_vers];
        for (int i = 0; i < num_vers; i++) {
            ver_array[i] = d[i];
        }
    }

    /**
     * @brief Destroy the adj List Grapgh object
     * 
     */
    ~adjListGrapgh() {
        edge_node *current;
        for (int i = 0; i < num_vers; i++) {
            current = ver_array[i].head;// FIX: assign current
            while (current != nullptr) {
                edge_node *next_ = current->next;
                delete current;
                current = next_;
            }
        }
        delete[] ver_array;
    }
    int num_ver() const {
        return num_vers;
    }
    int num_edge() const {
        return num_edges;
    }

    void insert(Ver x, Ver y, Edge w) {
        int u = find(x);
        int v = find(y);
        ver_array[u].head = new edge_node(v, w, ver_array[u].head);
        ++num_edges;
    }

    /**
     * @brief remove the edges from u to v
     * 
     * @param x 
     * @param y 
     */
    void remove(Ver x, Ver y) {
        int u = find(x);
        int v = find(y);
        edge_node *p = ver_array[u].head;

        if (p == nullptr) {
            // no edges
            return;
        }

        if (p->end == v) {
            // the first is the edge to be deleted
            ver_array[u].head = p->next;
            delete p;
            --num_edges;
            return;
        }

        while (p->next != nullptr && p->next->end != v) {
            p = p->next;
        }

        if (p->next != nullptr) {
            // p->next is the node to be deleted
            edge_node *q = p->next;
            p->next = q->next;
            delete q;
            --num_edges;
        }
    }

    bool exist(Ver x, Ver y) const {
        int u = find(x);
        int v = find(y);
        edge_node *p = ver_array[u].head;
        while (p != nullptr && p->end != v) {
            p = p->next;
        }
        if (p == nullptr) {
            return false;
        }
        return true;
    }

    // !Add new Function: Eulerian path
    std::vector<int> EulerCurcuit(Ver start) {
        std::vector<int> ans;
        if (num_edges == 0) {
            // absolutely there is no path
            // return the empty vector
            return ans;
        }

        for (int i = 0; i < num_vers; i++) {
            int num_of_degree = 0;
            edge_node *r = ver_array[i].head;
            while (r != nullptr) {
                ++num_of_degree;
                r = r->next;
            }

            if (num_of_degree % 2 != 0) {
                // no exists!
                return ans;
            }
        }

        int start_pos = find(start);

        // clone a list of adjlist
        ver_node *tmp = clone();
        Eulerian_Node *beg, *end;
        EulerCircuit(start_pos, beg, end);
        while (true) {
            Eulerian_Node *current = beg;
            while (current->next != nullptr) {
                // check the afterward nodes of current
                if (ver_array[current->next->Node_num].head != nullptr) {
                    break;
                } else {
                    current = current->next;
                }
            }

            // all the edges have been visited
            if (current->next == nullptr) {
                break;
            }

            Eulerian_Node *q = current->next;
            // q is the node yet to be visited

            // find a subcircle and connect them
            Eulerian_Node *tb, *te;
            EulerCircuit(q->Node_num, tb, te);
            te->next = q->next;
            current->next = tb;
        }


        delete[] ver_array;
        ver_array = tmp;


        Eulerian_Node *tmp_store;
        while (beg != nullptr) {
            ans.push_back(ver_array[beg->Node_num].value);
            tmp_store = beg;
            beg = beg->next;
            delete tmp_store;
        }
        return ans;
    }

    ver_node* clone() const {
        ver_node *tmp = new ver_node[num_vers];
        edge_node *p;

        for (int i = 0; i < num_vers; i++) {
            tmp[i].value = ver_array[i].value;
            p = ver_array[i].head;
            while (p != nullptr) {
                tmp[i].head = new edge_node(p->end, p->weight, tmp[i].head);
                p = p->next;
            }
        }
        return tmp;
    }
};

int main() {
    // Example: Eulerian circuit in an undirected graph
    // Vertices: 0, 1, 2, 3
    // Edges: 0-1, 1-2, 2-0, 0-3, 3-2 (all degrees are even)
    int vertices[] = {0, 1, 2, 3, 4, 5};
    int numVertices = sizeof(vertices) / sizeof(vertices[0]);
    adjListGrapgh<int, int> g(numVertices, vertices);

    // Insert undirected edges (add both directions)
    g.insert(0, 1, 1);
    g.insert(1, 0, 1);
    g.insert(1, 2, 1);
    g.insert(2, 1, 1);
    g.insert(2, 0, 1);
    g.insert(0, 2, 1);
    g.insert(0, 3, 1);
    g.insert(3, 0, 1);
    g.insert(3, 2, 1);
    g.insert(2, 3, 1);
    g.insert(1, 4, 2);
    g.insert(4, 1, 2);
    g.insert(1, 5, 6);
    g.insert(5, 1, 6);
    g.insert(0, 4, 5);
    g.insert(4, 0, 5);
    g.insert(2, 5, 3);
    g.insert(5, 2, 3);

    std::vector<int> path = g.EulerCurcuit(0);

    if (path.empty()) {
        std::cout << "No Eulerian circuit exists in the graph." << std::endl;
    } else {
        std::cout << "Eulerian circuit: ";
        for (int v : path) {
            std::cout << v << " ";
        }
        std::cout << std::endl;
    }

    return 0;
}
```

#### 哈密尔顿回路和旅行商问题

**哈密尔顿回路**：在无向图中找到一个回路，该回路通过每个节点一次且仅一次。

详见**未来算法：旅行商问题**。

### 有向图的强联通 (Kosaraju Algorithm)

- 从任意结点开始对有向图$G$进行深度优先搜索，得到一个深度优先森林。
- 将节点遍历：**按照生成树的次序 + 对每一棵树上的节点进行后序遍历**
- 将图$G$逆向得到$G_r$。
- 从编号最大的节点开始深度优先搜索$G_r$,得到的森林就是**原图的强联通分量**。

#### Thm

- $x$ is the root for the tree which contains $v$ in $G_r$, then $\exists x \to v \in G_r \text{ and } v \to x \in G$.
- $x$ is the root for the tree which contains $w$ in $G_r$, then $\exists x \to w \in G_r \text{ and } w \to x \in G$.

Then it suffices to prove:

- If $v$ and $w$ are in the same BFS tree for $G_r$, then $\exists v \to w \text{ and } w \to v \in G$.

Hence, we only need to prove:

$x$ is the root for the tree which contains $v$ in $G_r$, then $\exists x \to v \in G_r \text{ and } v \to x \in G$.

- For $x$ is the root node, there exists a path from $x$ to $v$ in $G_r$.
- For $x$ is the root node, the index for $x$ is greater than $v$. Hence, all work of $v$ will be finished before $x$.
- Hence, there exists a path from $v$ to $x$. ($v$ finishes before $x$)

## References

[^1]:[Harvard 50.3 Introduction to artificial intelligence](https://www.youtube.com/watch?v=WbzNRTTrX0g&list=PLhQjrBD2T381PopUTYtMSstgk-hsTGkVm&index=2)
