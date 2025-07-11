---
title: DataStructure-Tree-Binary-Heap
date: 2025-03-24 00:11:34
index_img: /img/cover/binaryheap.jpg
excerpt: Lecture notes for MIT6.006 Lec8 Binary Heaps.
mermaid: true
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Tree
  - Binary Heap
  - Data Structure
  - C/C++
  - Finished
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Data Structure: Binary Heap

The lecture note of **MIT6.006**, Lec8: Binary Heaps.

## Priority Queue Interface

- `build(X)`: Init to items in $X$.
- `insert(x)`: add item $x$.
- `delete_max()`: delete and return the max-key item.
- ` find_max()`: return max_key item.

Queue is a Linear List, but if I want to place different priorities into different elements, we need to use **Priority Queue** implemented by Tree!

![Heap Sort](https://s1.imagehub.cc/images/2025/03/27/e5276b2fe19a07a43d885bf4eb68f9d1.png)

For dynamic array, `insert` is max but `delete_max()` is too slow ($O(n)$)! However, in **Sorted Dynamic Array**, it is just the opposite. It's a tradeoff for inserting and deleting if you stick to the sequential data structure. However, we can use **Set AVL** for getting both operations $O(\log n)$ time complexity!

> **In place** means when sorting, the data structure doesn't need extra space (or $O(1)$ time complexity) to finish sorting. AVL set is very complex, where we need extra space to do several rotations in order to get the sorting.

## Priority Queue Sort

Time complexity $O(n\log n)$.

- insert $x$ for $x$ in array (A) (`build(A)`)
- using `delete_max` until the priority queue is empty.
- Then we get sorted for using the Priority Queue Sort.

## Arrays as a Complete Binary Tree

Consider the **complete binary tree**, there is a bijection (one to one correspondence) between this binary tree and an array traversed by a sequential order.

![Arrays as a complete binary tree](https://s1.imagehub.cc/images/2025/03/27/41df4ffab183b2adf0062c6d2473af1b.png)

# Data Structure Priority Queue

Lecture note for Data Structure in SJTU

## 基于线性表的优先级队列

如何处理优先级？

- 进队插入，出队直接出队队首元素（Dynamic Sorted Array）
- 进队尾端进队，出队查找优先级最高的元素出队 （Dynamic Array）

## 基于树的优先级队列

### Definition

堆可以分为两种主要类型：**最大堆（Max-Heap）** 和 **最小堆（Min-Heap）** ，它们的核心性质如下：

#### 最大堆（Max-Heap）

对于一个最大堆：

- 堆中的每个节点的值都**大于或等于** 其子节点的值。
- 数学上，设 $h[i]$ 表示堆中第 *i* 个节点的值，则对于任意节点 $i$ 满足以下条件：$h[i]≥h[2i+1]$和$h[i]≥h[2i+2]$其中 $2i+1$ 和 $2i+2$ 分别是节点 *i* 的左子节点和右子节点的索引。

#### 最小堆（Min-Heap）

对于一个最小堆：

- 堆中的每个节点的值都**小于或等于** 其子节点的值。
- 数学上，设 $h[i]$ 表示堆中第 *i* 个节点的值，则对于任意节点 $i$ 满足以下条件：$h[i]\le[2i+1]$和$h[i]\le h[2i+2]$其中 $2i+1$ 和 $2i+2$ 分别是节点 *i* 的左子节点和右子节点的索引。

- 二叉堆完全符合完全二叉树的结构性质，同时满足有序性。
- 由于二叉堆是完全二叉树，故可以使用顺序存储的形式来存储一个完全二叉树。

### 优先级队列的存储实现 二叉堆

二叉堆的数学性质保证了**根节点**始终是最大值和最小值。假设数值越大，优先级越高，故可以使用一个**最大化堆**来存储优先级队列。一旦保持了二叉堆的性质，我们只需要返回根节点就可以找到最优先的元素。因此，**二叉堆的重点是进队和出队的操作**，来保持二叉堆的有序性质和完全二叉树的操作。

#### 进队操作

- 满足结构性：添加在叶子结点，保证还是**完全二叉树**。
	- 在最大序号的元素之后
- 为了满足**有序性**：**新节点不断向上过滤（对于父亲比儿子小的情况）**，直到满足父亲比儿子大，满足有序性
	- 在向上过滤的过程中，优先级是递增的

#### 出队操作

在根节点出队后，为了保证结构性，把**最后一个节点**放进根节点，使其变成完全二叉树。

为了满足有序性，此处可以**向下过滤**，将两个儿子中更大的元素与父亲交换。

#### 建堆操作

- 递归调用左右子树
- 也可以**自下而上**，对每一个非叶结点向上过滤。

这样的建堆过程能够达到**线性时间复杂度**，证明见下：

{% note primary %}

![Linear Time Complexity](https://s1.imagehub.cc/images/2025/03/27/a178bcb6283355c12a333108f575cccf.png)

{% endnote %}

#### 代码实现

因此，我们给出二叉堆的具体实现，保持**完全二叉树的有序性和结构性**：

```cpp
#include <iostream>

template <class T>
class priorityQueue {
private:
    size_t currentsize;
    T* array;
    size_t maxsize;

    void doublespace() {
        T* tmp = array;
        maxsize *= 2;
        array = new T[maxsize];
        for (size_t i = 0; i <= currentsize; ++i) {
            array[i] = tmp[i];
        }
        delete[] tmp;
    }

    void buildheap() {
        for (size_t i = currentsize / 2; i >= 1; --i) {
            percolateDown(i);
        }
    }

    void percolateDown(size_t hole) {
        size_t child = 0;
        T tmp = array[hole];

        while (hole * 2 <= currentsize) {
            child = hole * 2;
            if (child < currentsize && array[child + 1] < array[child]) {
                ++child;
            }

            if (array[child] < tmp) {
                array[hole] = array[child];
                hole = child;
            } else {
                break;
            }
        }
        array[hole] = tmp;
    }

public:
    priorityQueue(size_t capacity = 100) : currentsize(0), maxsize(capacity) {
        array = new T[maxsize];
    }

    ~priorityQueue() {
        delete[] array;
    }

    priorityQueue(const T* data, size_t size) : currentsize(size), maxsize(size + 10) {
        array = new T[maxsize];
        for (size_t i = 0; i < size; ++i) {
            array[i + 1] = data[i];
        }
        buildheap();
    }

    bool empty() const {
        return currentsize == 0;
    }

    void enQueue(const T& x) {
        if (currentsize == maxsize - 1) {
            doublespace();
        }

        size_t hole = ++currentsize;
        while (hole > 1 && x < array[hole / 2]) {
            array[hole] = array[hole / 2];
            hole /= 2;
        }
        array[hole] = x;
    }

    T deQueue() {
        if (empty()) {
            throw std::underflow_error("Priority queue is empty");
        }

        T minItem = array[1];
        array[1] = array[currentsize--];
        percolateDown(1);
        return minItem;
    }

    T getHead() const {
        if (empty()) {
            throw std::underflow_error("Priority queue is empty");
        }
        return array[1];
    }
};

template <typename T>
void pqsort(T* unsorted, T* sorted, size_t size) {
    priorityQueue<T> pq(unsorted, size);
    for (size_t i = 0; i < size; ++i) {
        sorted[i] = pq.deQueue();
    }
}

int main() {
    int size = 5;
    int unsorted[] = {1, 4, 2, 6, 3};
    int* sorted = new int[size];

    try {
        pqsort(unsorted, sorted, size);

        for (size_t i = 0; i < size; ++i) {
            std::cout << sorted[i] << std::endl;
        }
    } catch (const std::exception& e) {
        std::cerr << "Error: " << e.what() << std::endl;
    }

    delete[] sorted;
    return 0;
}
```

> 同时，我们利用priority queue实现了priority queue sort函数，通过对原始数组建立**二叉堆**，接着不断的删除根节点元素，得到排序后的元素（in the Ascending order），因为这里是一个最小堆。

### D堆

延伸：每个节点有$D$个儿子。

当D较大时，对于入队操作，二叉树的高度更低，因此**向上过滤**的速度更快（每个儿子只有一个爸爸），但是代价是对于出队操作，**需要查找的节点数上升**，导致**向下过滤**的速度变慢。并且在二叉树中，移位的操作是依靠位运算（乘2或除2）来实现的，速度较快，而如果D不是2的幂次。

使用二叉堆能够高效地实现优先级队列，但是二叉堆结构实现非常的严格，必须要同时满足**完全二叉树的结构性**和最大（最小）的**排序性**。

如果在不考虑结构性的情况下仍然保证**有序性**？我们就可以产生各种各样的堆！

由于**不再保持完全二叉树的严格结构**，我们可以生成很多很多个堆，因此此时，**堆之间的合并**是非常重要的操作。

D堆的作用：

- 对于**插入操作远多于删除操作**的情况

- 对于**较大的优先级队列导致无法装入内存的情况**，D堆可以减少内外存的交换。

	- D堆的每个节点有 $D$个子节点，因此每个节点可以表示更多的信息。当$D$较大时，堆的宽度增加，而高度降低。这使得堆的每一层可以更好地适应内存分页机制：
		- 更少的层数意味着更少的页面切换。
		- 每次访问的节点更集中，减少了跨页访问的概率。

	- 减少了内外存之间的数据交换次数，提升了整体性能。

## 归并优先级队列

### 左堆

#### Definition

- **空路径长度 $npl$ **：x到**不满两个个儿子节点的最短路径**。
	- 具有1个或0个儿子节点的`npl`为0
	- 空节点（`nullptr`）的$npl = -1$
- **左堆**保证左儿子的$npl$ >= 右儿子的$npl$

> 形象来说，就是不满足平衡性（完全二叉树）但是向左倾斜的堆。

#### 左堆的递归归并

我们实现**对两个子堆的合并操作**：

- 将**根节点稍大的堆**与另一个堆的右子树进行归并。
- 如果产生的堆违反了左堆的定义，则交换左右子树（**这一步在“归”的步骤上实现**）
- 递归的终止条件：**当某个堆为空，另一个堆就是归并的结果**。

{% note info %}

1. 假设 $H_1$ 的根节点值小于等于 $H_2$ 的根节点值（不失一般性）。
2. 将$H_2$与 $H_1$ 的右子树进行递归合并，结果是一个新的右子树。
3. 将这个新的右子树重新赋值给 $H_1.right$。
4. 检查并调整 $H_1$ 的左偏性质（即确保左子树的零路径长度大于等于右子树的零路径长度）。
5. 更新 $H_1$ 的零路径长度

{% endnote %}

![Merge](https://s1.imagehub.cc/images/2025/03/27/d89ff689497c6fa68f24eb8157021e9d.png)

#### 左堆进队和出队

- 看做归并的特例（单元素堆）

- 删除根节点后再**归并**两个堆。

#### 代码实现

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-03-27 17:25:03
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-03-27 17:39:34
 * @FilePath: /Data_structure/single_files/Class_implementation/LeftistHeap.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>

struct LeftistNode {
    int key;
    int npl; // Null Path Length
    LeftistNode* left;
    LeftistNode* right;

    LeftistNode(int k) : key(k), npl(0), left(nullptr), right(nullptr) {}
};

class LeftistHeap {
public:
    LeftistHeap() : root(nullptr) {}

    void insert(int key) {
        LeftistNode* newNode = new LeftistNode(key);
        root = merge(root, newNode);
    }

    void deleteMin() {
        if (!root) return;
        LeftistNode* temp = root;
        root = merge(root->left, root->right);
        delete temp;
    }

    int getMin() const {
        if (!root) throw std::runtime_error("Heap is empty!");
        return root->key;
    }

private:
    LeftistNode* root;

    LeftistNode* merge(LeftistNode* h1, LeftistNode* h2) {
        if (!h1) return h2;
        if (!h2) return h1;

        if (h1->key > h2->key) std::swap(h1, h2);

        h1->right = merge(h1->right, h2);

        if (!h1->left || h1->left->npl < h1->right->npl) {
            std::swap(h1->left, h1->right);
        }

        h1->npl = h1->right ? h1->right->npl + 1 : 0;
        return h1;
    }
};

int main(){
    const int size = 10;
    int unsorted [size] = {1,4,6,7,2,3,5,8,92,31};

    //creating a new leftist heap
    LeftistHeap lfh;
    for(int i = 0; i < size; ++i){
        lfh.insert(unsorted[i]);
    }

    int* sorted = new int [size];
    for(int i = 0; i < size; ++i){
        sorted[i] = lfh.getMin();
        lfh.deleteMin();
    }

    for(int i = 0; i < size; i++){
        std::cout << sorted[i] << std::endl;
    }

    delete [] sorted;

}
```

### 斜堆

左堆对完全二叉树的严格显示做了适当的宽松，但是代价是**仍然需要维护左堆的性质（即维护每个结点的npl）**。我们可以进一步简化，于是我们可以构造**斜堆**来实现**没有任何平衡条件限定**的二叉树实现。

斜堆是**仅满足有序性不满足任何结构性的二叉树**。最坏时间复杂度为$O(N)$，平均时间复杂度为$O(\log N)$。（这一点会在后面的代码中详细分析）

斜堆相当于是一个**自调整的左堆**，在归并的过程中，无需判断是否满足左堆，但是在**归**的过程中**左右子树的交换是必须的**。并且也无需维护`npl`。

具体而言，在斜堆的归并操作中：

- **将根节点较大的堆和根节点较小的右子堆**归并。（这一点和左堆完全相同），作为新的右子堆。
- 递归终止条件：两个需要合并的堆中有一个为空。
- **每一次归并完成后**需要将归并起来的堆的左右子树交换。

为什么要实现第三点？因为交换左右子树本质上是实现一种**洗牌的操作**，否则斜堆很有可能会退化为二叉链表类（这样的退化情况会让$O(h)$退化为$O(n)$，是我们最不想看到的）

> 这一点从**感觉**上来说非常好理解，因为我们相当于在更新$H_2$的右节点，这样会导致**右侧的子树**比左边的更大，因此为了保证平衡，我们需要不断的交换左右节点。因为只有平衡二叉树才能保证$O(h)=O(\log n)$，否则会出现退化链表的情况。 

#### 代码实现

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-03-27 19:10:30
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-03-27 19:13:34
 * @FilePath: /Data_structure/single_files/Class_implementation/skewHeap.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */

#include <iostream>
struct SkewNode {
    int key;
    SkewNode* left;
    SkewNode* right;

    SkewNode(int k) : key(k), left(nullptr), right(nullptr) {}
};

class SkewHeap {
public:
    SkewHeap() : root(nullptr) {}

    void insert(int key) {
        SkewNode* newNode = new SkewNode(key);
        root = merge(root, newNode);
    }

    void deleteMin() {
        if (!root) return;
        SkewNode* temp = root;
        root = merge(root->left, root->right);
        delete temp;
    }

    int getMin() const {
        if (!root) throw std::runtime_error("Heap is empty!");
        return root->key;
    }

private:
    SkewNode* root;

    SkewNode* merge(SkewNode* h1, SkewNode* h2) {
        if (!h1) return h2;
        if (!h2) return h1;

        if (h1->key > h2->key) std::swap(h1, h2);

        // Swap the children of h1
        h1->right = merge(h1->right, h2);
        std::swap(h1->left, h1->right);

        return h1;
    }
};


int main(){
    const int size = 10;
    int unsorted [size] = {1,4,6,7,2,3,5,8,92,31};

    //creating a new leftist heap
    SkewHeap skh;
    for(int i = 0; i < size; ++i){
        skh.insert(unsorted[i]);
    }

    int* sorted = new int [size];
    for(int i = 0; i < size; ++i){
        sorted[i] = skh.getMin();
        skh.deleteMin();
    }

    for(int i = 0; i < size; i++){
        std::cout << sorted[i] << std::endl;
    }

    delete [] sorted;

}
```

左堆和斜堆在代码实现上都是比较简单的，因为都是递归函数。但是笔者在初学的时候不仅产生了如下的疑惑：

- 二叉堆那么完美，为什么我们希望这个堆保持**左倾**？（左堆）
- **归并的本质**就是让两个堆在**合并**只会仍然保持**堆的两条性质：有序性（这是优先级队列的核心）和 结构性**，那为什么在斜堆中我们需要强制交换**左右子树**呢？

{% note primary %}

#### 左堆相对于斜堆有什么优势？

- **左堆** ：左堆的合并、插入和删除最小值操作的时间复杂度为 *O*(log*n*)，这是一个确定性的上界。这种确定性使得左堆在对性能要求严格的场景中更有优势。
- **斜堆** ：斜堆的合并、插入和删除最小值操作的均摊时间复杂度也是 *O*(log*n*)，但这只是均摊意义上的结果。在最坏情况下，斜堆的操作可能会退化到 *O*(*n*)。

**优势总结** ：

- 左堆提供了更强的时间复杂度保证，适合需要稳定性能的场景。
- 斜堆虽然在均摊意义上与左堆相当，但在最坏情况下可能表现不佳。

| 特性       | 左堆                       | 斜堆                          |
| ---------- | -------------------------- | ----------------------------- |
| 时间复杂度 | 确定性*O*(log*n*)          | 均摊*O*(log*n*)，最坏*O*(*n*) |
| 实现复杂度 | 较高（需要维护零路径长度） | 较低（无需额外信息）          |
| 空间开销   | 较高（需要存储额外信息）   | 较低                          |


{% endnote %}

### 二项堆

我们可以再大胆一点！如果我们不使用二叉树，而是用广义的树进行储存呢？下面介绍**二项堆**，使用广义的树实现储存。

二项树构成的森林：

- 每一颗二项树都满足**堆的有序性**。
- 高度为$k$的二项树$B_{k-1}$是$B_{k-2}$加到另一棵$B_{k-2}$的根上形成的。

**二项树不是二叉树**。

![Binomial heap](https://s1.imagehub.cc/images/2025/03/27/643bdda39558c376a137d459c2f29c60.png)

#### 基于二项树的优先级队列表示

也就是说，我们可以使用**二项树**的集合（也就是森林）来实现优先级队列的存储。首先对于给定的元素个数$k$，根据二进制原理，可以使用二进制编码编码$k$，如果第$i$为是1，那么二项树$B_i$就在森林中，可以储存$2^i$个元素。这样就可以把每一个元素都填入到这个优先级队列中！

因为每一个二项树都满足有序性，因此只需要**挑选所有二项树**的根节点的最大值（或者最小值），就可以找到最大元素或者最小元素。因此，**二项树**实现的最关键环节还是**归并操作**！

对于**归并操作**，非常类似于二进制的加法。首先明确**队列中元素的个数**可以唯一确定**不同高度子树的分布情况**，因此一旦队列的元素个数发生了改变（删除元素或者插入元素），那么此时对应的二进制编码发生了改变，我们需要**对某些树进行操作**，使其高度发生变化。下面考虑两个二项堆发生归并的情况：

- 从低到高归并两个优先级队列中相同的树
	- 如果是两棵树，**把根节点值大的作为根节点值小的树的子树**，这样2个$B_k$就可以合并成一个$B_{k+1}$，满足有序性。
	- 如果是三棵树，随机选择两颗合并即可。
	- **关键就是保持相同高度二项树**只能为1或0。

对于入队和出队操作，同样也可以看做归并的特殊情况。此处不再具体分析。

#### 归并的时间复杂度

对于归并运算，相当于**二进制的进位过程**，因此，最倒霉的情况是每个高度都发生了进位。

##### 最坏情况分析

在最坏的情况下相当于$B_0, B_1, B_2, \dots, B_k$全部存在，这个时候在加入一个单节点就会不断进位，此时元素总数为：

$$n=2^0+2^1+2^2+2^3+\dots+2^k=2^{k+1} - 1$$

也就是说，此时时间复杂度是$O(\log n)$级别的。

##### 平均情况分析

- $B_0, B_1, B_2, \dots, B_k$ 出现的概率均为 $1/2$，所以
- $B_0, B_1$ 同时出现的概率为 $1/4$，归并 2 次
- $B_0, B_1, B_2$ 同时出现的概率为 $1/8$，归并 3 次，$\dots$

于是，平均情况的时间复杂度为：
$$T = \sum_{i=1}^{K} i \cdot (1/2)^i \\$$

使用错位相减法，可以得到：

$$T = 2 - (1/2)^K - K \cdot (1/2)^K < 2$$

**也就是说，归并的平均时间复杂度是常数级别的！**

#### 二项堆的时间性能

##### 归并
- $N$ 元素队列至多有 $\log N + 1$ 棵树，每两棵树归并只需要常量$O(1)$的常数时间复杂度，
- 最坏情况时间复杂度为 $O(\log N)$

##### 进队
- 平均情况时间复杂度为 $O(1)$

##### 出队
- 找出根结点值最小的树，耗时 $O(\log N) / O(\log N)$
- 归并若干个优先级队列，耗时 $O(1) / O(\log N)$
- 平均/最坏时间复杂度是 $O(\log N) / O(\log N)$ 的

#### 代码实现

```cpp
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

struct BinomialNode {
    // Node structure for storing the tree, using linked storage
    int key;                // Key value of the node
    int degree;             // Degree of the binomial tree (number of children)
    BinomialNode* parent;   // Pointer to the parent node
    BinomialNode* child;    // Pointer to the first child node
    BinomialNode* sibling;  // Pointer to the next sibling node

    BinomialNode(int k) : key(k), degree(0), parent(nullptr), child(nullptr), sibling(nullptr) {}
};

class BinomialHeap {
public:
    BinomialHeap() : head(nullptr) {}

    // Insert a new key into the heap
    void insert(int key) {
        BinomialNode* newNode = new BinomialNode(key); // Create a new node with the given key
        BinomialHeap tempHeap;                        // Create a temporary heap
        tempHeap.head = newNode;                      // Set the new node as the head of the temporary heap
        merge(tempHeap);                              // Merge the temporary heap with the current heap
    }

    // Delete the minimum key from the heap
    void deleteMin() {
        if (!head) return; // If the heap is empty, do nothing

        BinomialNode* minNode = head; // Initialize minNode with the head of the heap
        BinomialNode* prev = nullptr; // Pointer to the previous node of minNode
        BinomialNode* curr = head;    // Pointer to traverse the heap

        // Find the minimum node in the root list
        while (curr) {
            if (curr->key < minNode->key) {
                minNode = curr;
                prev = curr;
            }
            curr = curr->sibling;
        }

        // Remove the minimum node from the root list
        if (prev) {
            prev->sibling = minNode->sibling; // Bypass the minNode
        } else {
            head = minNode->sibling; // Update the head if minNode is the first node
        }

        // Reverse the child list of minNode to create a new heap
        BinomialHeap tempHeap;
        tempHeap.head = reverseChildren(minNode->child);

        // Merge the new heap with the original heap
        merge(tempHeap);

        delete minNode; // Free the memory of the deleted node
    }

    // Get the minimum key in the heap
    int getMin() const {
        if (!head) {
            throw std::runtime_error("Heap is empty!"); // Throw an exception if the heap is empty
        }
        int minVal = head->key; // Initialize minVal with the key of the head node
        for (BinomialNode* curr = head; curr; curr = curr->sibling) {
            minVal = min(minVal, curr->key); // Traverse the root list to find the minimum key
        }
        return minVal;
    }

private:
    BinomialNode* head; // Pointer to the head of the root list

    // Reverse the child list of a node
    BinomialNode* reverseChildren(BinomialNode* node) {
        BinomialNode* prev = nullptr; // Pointer to the previous node
        BinomialNode* curr = node;    // Pointer to traverse the child list
        while (curr) {
            BinomialNode* next = curr->sibling; // Store the next sibling
            curr->sibling = prev;               // Reverse the sibling pointer
            curr->parent = nullptr;            // Clear the parent pointer
            prev = curr;                        // Move prev to the current node
            curr = next;                        // Move curr to the next node
        }
        return prev; // Return the new head of the reversed list
    }

    // Merge two binomial heaps
    void merge(BinomialHeap& other) {
        BinomialNode* newHead = nullptr;      // New head of the merged heap
        BinomialNode** tail = &newHead;       // Pointer to the tail of the merged heap
        BinomialNode* h1 = head;              // Pointer to traverse the first heap
        BinomialNode* h2 = other.head;        // Pointer to traverse the second heap

        // Merge the root lists of the two heaps
        while (h1 && h2) {
            if (h1->degree < h2->degree) {    // Compare degrees and add the smaller one to the merged list
                *tail = h1;
                h1 = h1->sibling;
            } else {
                *tail = h2;
                h2 = h2->sibling;
            }
            tail = &((*tail)->sibling);       // Move the tail pointer forward
        }

        // Append the remaining nodes from either heap
        *tail = h1 ? h1 : h2;
        head = newHead;                       // Update the head of the merged heap

        consolidate();                        // Consolidate the merged heap to ensure unique degrees
    }

    // Consolidate the heap to ensure each degree appears only once
    void consolidate() {
        vector<BinomialNode*> degreeTable(32, nullptr); // Table to store nodes by degree (max degree assumed to be 32)

        BinomialNode* curr = head;            // Pointer to traverse the heap
        BinomialNode* next;

        while (curr) {
            next = curr->sibling;             // Store the next sibling
            while (degreeTable[curr->degree]) { // Check if there is another node with the same degree
                BinomialNode* other = degreeTable[curr->degree]; // Get the other node
                if (curr->key > other->key) swap(curr, other);   // Ensure curr has the smaller key
                link(other, curr);                               // Link the two nodes
                degreeTable[curr->degree - 1] = nullptr;         // Clear the previous degree entry
            }
            degreeTable[curr->degree] = curr; // Store the current node in the table
            curr = next;                      // Move to the next node
        }

        head = nullptr;                       // Reset the head of the heap
        for (auto node : degreeTable) {       // Rebuild the root list from the degree table
            if (node) {
                node->sibling = head;         // Add the node to the front of the root list
                head = node;
            }
        }
    }

    // Link two binomial trees together
    void link(BinomialNode* child, BinomialNode* parent) {
        child->parent = parent;               // Set the parent of the child node
        child->sibling = parent->child;       // Add the child to the front of the parent's child list
        parent->child = child;                // Update the parent's child pointer
        parent->degree++;                     // Increment the degree of the parent
    }
};

// Test the BinomialHeap class
int main() {
    try {
        BinomialHeap bh;

        // Insert elements into the heap
        bh.insert(10);
        bh.insert(5);
        bh.insert(7);
        bh.insert(3);
        bh.insert(8);

        // Print the minimum element
        cout << "Minimum element: " << bh.getMin() << endl;

        // Delete the minimum element and print the new minimum
        bh.deleteMin();
        cout << "Minimum element after deletion: " << bh.getMin() << endl;

        // Delete the minimum element again and print the new minimum
        bh.deleteMin();
        cout << "Minimum element after second deletion: " << bh.getMin() << endl;

    } catch (const exception& e) {
        cerr << "Error: " << e.what() << endl;
    }

    return 0;
}
```

## Priority Queue in STL

在头文件`queue`中，存在使用二叉堆实现的类模板容器`priority_queue`，其有三个模版参数：

- 队列中的元素类型
- 底层储存二叉堆的容器类型（数组，因为是完全二叉树）
- 最大堆还是最小堆

`std::priority_queue` 是 C++ 标准模板库（STL）中的一个容器适配器，用于实现优先队列。优先队列是一种特殊的队列，其中每个元素都有一个优先级，优先级高的元素会被优先处理。默认情况下，`std::priority_queue` 是一个最大堆，即优先级最高的元素（即最大值）位于队列的顶部。

### 自定义比较函数
默认情况下，`std::priority_queue` 是最大堆。如果想实现最小堆或其他自定义排序规则，可以通过自定义比较函数来实现。

#### 最小堆示例：
```cpp
#include <queue>
#include <vector>

// 使用 std::greater 实现最小堆
std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;

minHeap.push(10);
minHeap.push(30);
minHeap.push(20);

std::cout << "Top element: " << minHeap.top() << std::endl;  // 输出 10
```

#### 自定义比较函数示例：
```cpp
struct CustomCompare {
    bool operator()(const int& lhs, const int& rhs) const {
        return lhs > rhs;  // 最小堆
    }
};

std::priority_queue<int, std::vector<int>, CustomCompare> customPQ;

customPQ.push(10);
customPQ.push(30);
customPQ.push(20);

std::cout << "Top element: " << customPQ.top() << std::endl;  // 输出 10
```

## Application: The simulation of Queueing system

> TBD

## Extension: Fibonacci Heap

**Powered By GPT-4o**

斐波那契堆（Fibonacci Heap）是一种高效的优先队列数据结构，由Michael L. Fredman 和 Robert E. Tarjan 在1984年提出。它在许多操作上具有优秀的渐进时间复杂度，特别适合需要频繁插入和合并的场景。

1. **高效的操作时间复杂度**：
   - 插入（Insert）：O(1) 摊还时间。
   - 合并（Union）：O(1) 摊还时间。
   - 查找最小值（Find-Min）：O(1) 时间。
   - 删除最小值（Extract-Min）：O(log n) 摊还时间。
   - 减小键值（Decrease-Key）：O(1) 摊还时间。
   - 删除节点（Delete）：O(log n) 摊还时间。

2. **松散的树形结构**：
   - 斐波那契堆由一组最小堆有序树组成，这些树之间没有严格的形状约束（与二叉堆不同）。
   - 树之间的合并和拆分非常灵活，这使得斐波那契堆在某些操作中表现优异。

3. **延迟合并策略**：
   - 许多操作（如删除最小值）会延迟执行实际的结构调整，直到必要时才进行清理（consolidation），从而摊还时间复杂度更低

```cpp
#include <iostream>
#include <vector>
#include <cmath>

struct Node {
    int key;
    int degree = 0; // 子节点的数量
    bool marked = false; // 是否失去了一个子节点
    Node* parent = nullptr;
    Node* child = nullptr; // 指向第一个子节点
    Node* left = nullptr; // 左兄弟
    Node* right = nullptr; // 右兄弟

    explicit Node(int k) : key(k), left(this), right(this) {}
};

class FibonacciHeap {
private:
    Node* minNode = nullptr; // 指向当前最小节点
    int nodeCount = 0;

    void consolidate() {
        int maxDegree = static_cast<int>(std::log2(nodeCount)) + 1;
        std::vector<Node*> degreeTable(maxDegree, nullptr);

        std::vector<Node*> roots;
        Node* current = minNode;
        if (current) {
            do {
                roots.push_back(current);
                current = current->right;
            } while (current != minNode);
        }

        for (Node* x : roots) {
            int d = x->degree;
            while (degreeTable[d] != nullptr) {
                Node* y = degreeTable[d];
                if (x->key > y->key) {
                    std::swap(x, y);
                }
                link(y, x);
                degreeTable[d] = nullptr;
                d++;
            }
            degreeTable[d] = x;
        }

        minNode = nullptr;
        for (Node* root : degreeTable) {
            if (root) {
                if (!minNode || root->key < minNode->key) {
                    minNode = root;
                }
            }
        }
    }

    void link(Node* y, Node* x) {
        // Remove y from the root list
        y->left->right = y->right;
        y->right->left = y->left;

        // Make y a child of x
        y->parent = x;
        if (!x->child) {
            x->child = y;
            y->left = y;
            y->right = y;
        } else {
            y->left = x->child;
            y->right = x->child->right;
            x->child->right->left = y;
            x->child->right = y;
        }
        x->degree++;
        y->marked = false;
    }

    void addChild(Node* parent, Node* child) {
        if (!parent->child) {
            parent->child = child;
            child->left = child;
            child->right = child;
        } else {
            child->left = parent->child;
            child->right = parent->child->right;
            parent->child->right->left = child;
            parent->child->right = child;
        }
        child->parent = parent;
        parent->degree++;
    }

public:
    FibonacciHeap() = default;

    bool isEmpty() const {
        return minNode == nullptr;
    }

    Node* insert(int key) {
        Node* newNode = new Node(key);
        if (!minNode) {
            minNode = newNode;
        } else {
            // Add to root list
            newNode->left = minNode;
            newNode->right = minNode->right;
            minNode->right->left = newNode;
            minNode->right = newNode;

            if (newNode->key < minNode->key) {
                minNode = newNode;
            }
        }
        nodeCount++;
        return newNode;
    }

    int findMin() const {
        if (!minNode) throw std::runtime_error("Heap is empty");
        return minNode->key;
    }

    int extractMin() {
        Node* z = minNode;
        if (z) {
            if (z->child) {
                Node* child = z->child;
                do {
                    Node* nextChild = child->right;
                    // Add child to root list
                    child->left = minNode;
                    child->right = minNode->right;
                    minNode->right->left = child;
                    minNode->right = child;
                    child->parent = nullptr;
                    child = nextChild;
                } while (child != z->child);
            }

            // Remove z from root list
            z->left->right = z->right;
            z->right->left = z->left;

            if (z == z->right) {
                minNode = nullptr;
            } else {
                minNode = z->right;
                consolidate();
            }
            nodeCount--;
            int key = z->key;
            delete z;
            return key;
        }
        throw std::runtime_error("Heap is empty");
    }

    ~FibonacciHeap() {
        if (minNode) {
            std::vector<Node*> stack;
            stack.push_back(minNode);
            while (!stack.empty()) {
                Node* current = stack.back();
                stack.pop_back();
                Node* child = current->child;
                if (child) {
                    Node* temp = child;
                    do {
                        stack.push_back(temp);
                        temp = temp->right;
                    } while (temp != child);
                }
                delete current;
            }
        }
    }
};

int main() {
    FibonacciHeap heap;
    heap.insert(10);
    heap.insert(5);
    heap.insert(20);
    heap.insert(1);

    std::cout << "Minimum: " << heap.findMin() << std::endl; // 输出 1
    std::cout << "Extract Min: " << heap.extractMin() << std::endl; // 输出 1
    std::cout << "New Minimum: " << heap.findMin() << std::endl; // 输出 5

    return 0;
}
```



1. **节点结构**：
   - 每个节点包含 `key` 值、度数（`degree`）、标记状态（`marked`）、父节点指针（`parent`）、子节点指针（`child`）以及左右兄弟指针（`left` 和 `right`）。

2. **核心操作**：
   - `insert`：将新节点插入根链表，并更新最小节点。
   - `findMin`：返回当前最小节点的键值。
   - `extractMin`：删除最小节点，并重新调整堆结构。
   - `consolidate`：通过合并相同度数的树来优化堆结构。

3. **辅助函数**：
   - `link`：将一个节点作为另一个节点的子节点。
   - `addChild`：将子节点添加到父节点的子链表中。

4. **析构函数**：
   - 递归释放所有节点的内存，避免内存泄漏。

