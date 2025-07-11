---
title: DataStructure Segment Tree
date: 2025-04-20 22:07:06
index_img: /img/cover/segtree.jpg
excerpt: This article focuses on the classic interval query and modification algorithm, the segment tree.
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Segment Tree
  - Data Structure
  - Finished
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# 数据结构：线段树 (Segment Tree)

线段树虽迟但到！！！

## Introduction

线段树和**分块思想，树状数组**等数据结构非常类似，但是功能更加的强大。他可以在$O(\log N)$的时间复杂度内实现各种**区间操作**：区间修改和区间查询操作（当然退化一下就是单点修改和查询操作~）

## Intuition

线段树的结构和树状数组的结构非常类似，我们来看下面的图：

**树状数组**：

![树状数组](https://oi-wiki.org/ds/images/fenwick.svg)

**线段树**：

![线段树](https://hexo-rxy.oss-cn-beijing.aliyuncs.com/data_structure/segmentTree/%E7%BA%BF%E6%AE%B5%E6%A0%91%E8%A1%A8%E7%A4%BA.png)

> 图源：http://redmapleren.com/2020/03/30/%E5%86%85%E5%8A%9F%E4%BF%AE%E7%82%BC-%E7%BA%BF%E6%AE%B5%E6%A0%91%EF%BC%88%E4%B8%80%EF%BC%89/

比较两种树结构，我们不难发现：

- 线段树是一个严格的二叉树（有时为了方便分析，我们会将树扩充成满二叉树）
- **线段树的分化**是基于递归分治的思想实现的。

## Build a Tree!

给出如下的线段树模版：

{% note danger %}

**注意：此处下标全部采用1-based的实现**。

{% endnote %}

> 图源：[OI wiki](https://oi-wiki.org/ds/seg/)

![线段树建树](https://oi-wiki.org/ds/images/segt1.svg)

描述建树过程，就是给定原数组$a[i]$($i = 1,2,\dots,5$)，建立一颗二叉树（可以使用数组存储$d[i]$），其中每一个$d[i]$都**管辖特定的区间范围（管辖区间和）**。因为这里是一颗完全二叉树，显然**满足下标性质**：

$$\text{leftchild}(d_i) = d_{2i}, \text{rightchild}(d_i) = d_{2i + 1}$$

因为**线段树的本质是分治问题**，因此我们考虑**分割区间的时候使用对半分的方式**，知道每一个区间都只有一个元素。因此，我们还有如下的性质：(**管辖区间**)

$$d_i \to [s,t] \\\\ d_{2i} \to [s, [\frac{s + t}{2}]], \ d_{2i+1} \to [ [\frac{s + t}{2}]+1, t]$$

因此，我们考虑**递归建树的过程**。

### 数组开多大？

为了方便，我们直接开数组来存储线段树的节点，我们考虑满二叉树的情况，则此时$H = \log_{2} (N)$，$N$为原数组的大小，$H$为建树之后的高度。我们也可以使用递推式来计算：

$$N(n) = 2N(\frac{n}{2}) + 1$$

因此，$N(n) \approx 2n$

为了方便我们直接开数组为$4N$，保证够用就行。

我们可以给出建树的具体代码：

```cpp
#include <cstring>
#include <cmath>

const int MAXN = 10000;
int arr[MAXN];
class segmentTree {
    struct TreeNode {
        int left, right;
        int lson, rson;
        long long data;
    };

private:
    TreeNode seg_arr[4 * MAXN];

public:
    segmentTree() {
        // build the initial tree (all empty)
        std::memset(seg_arr, 0, sizeof(arr));
    }

    void build(int number, int s, int t) {
        // build the tree
        seg_arr[number].left = s;
        seg_arr[number].right = t;
        seg_arr[number].lazy_tag = 0;
        seg_arr[number].lson = number << 1;
        seg_arr[number].rson = seg_arr[number].lson + 1;


        if (s == t) {
            // finish the end, return
            seg_arr[number].data = arr[s];
            return;
        }

        // not finish, go deeper
        int mid = (s + t) / 2;
        build(seg_arr[number].lson, s, mid);
        build(seg_arr[number].rson, mid + 1, t);

        // update the value of the father node
        push_up(number);
    }

    void push_up(int node) {
        auto &cur = seg_arr[node];
        cur.data = std::max(seg_arr[cur.lson].data,
                       seg_arr[cur.rson].data);
    }
};
```

> `push_up`在递归函数建树的**回溯阶段**执行，这里维护的信息是区间的最大值，因此取>两个孩子的最大就行。（如果要维护其他的信息需要额外的做调整）。

## 区间查询

下面我们来进行批量区间查询：即在单次询问的过程中，给定一个区间$[l,r]$(1-based)，查询**区间中的最大值**。

思路很简单，因为**线段树维护了不同层级的节点代表不同的长度**，我们希望这些长度的节点可以**恰好覆盖**我们需要查询的区间，然后取每一个区间的预处理就可以了。（**和倍增的思路非常类似！但这里并不一定是2的幂次**）

这里的算法实现比树状数组要简单很多！（因为程序的核心都是**递归的模拟**）

```cpp
int query(int node, int l, int r) {
        auto &cur = seg_arr[node];
        if (l <= cur.left && cur.right <= r) {
            // be totally covered, just return as the end of the recursion
            return cur.data;
        }
        auto mid = (cur.left + cur.right) >> 1;
        int res = 0;
        if (l <= mid) {
            res = std::max(res, query(cur.lson, l, r));
        }
        if (r > mid) {
            res = std::max(res, query(cur.rson, l, r));
        }
        return res;
    }
```

代码思想：

- 首先从根节点1开始，判断这个节点管辖的范围是否**完全被区间覆盖**，如果是，则直接返回（**后续的回溯过程**），如果不是，分裂成左右两个节点，递归判断：
	- 如果`l <= mid`：说明**查询区间和左孩子的管辖区间存在交集**，因此需要递归查询左孩子。
	- 同时，我们也需要判断并检查**右孩子**。
- 最终回溯过程结束，返回。

## 单点修改

思路：**从根节点一路向下一直走到需要被修改的节点**（因为**叶子结点所管辖的区间范围就是单点**）

不过最关键的是**向上回溯的部分**：因为我们需要**保证上游的祖先节点的信息也要被更新**。

```cpp
void update(int node, int pos, int val) {
        auto &cur = seg_arr[node];
        auto mid = (cur.left + cur.right) >> 1;
        if (cur.lson == cur.rson) {
            // find the leaf node, return!
            cur.data = val;
            return;
        }
        if (pos <= mid) {
            update(cur.lson, pos, val);
        }
        if (pos > mid) {
            update(cur.rson, pos, val);
        }
        push_up(node);
    }
```

在上文的代码中，如果目标位置`pos`比中间值小，说明**分支出现在左孩子的路上**，反之同理。同样，这里也需要**向上回溯**更新（使用`push_up()`即可）

## 区间修改

现在我们来到了最后一种模版操作：**区间修改**。

我们需要借鉴分块标`tag`的思想，引入**懒标记**，来避免不必要的更新操作。

懒惰标记，简单来说，就是通过**延迟对节点信息的更改**，从而减少可能不必要的操作次数。每次执行修改时，我们通过打标记的方法表明该节点**对应的区间在某一次操作中被更改，但不更新该节点的子节点的信息**。实质性的修改则在下一次访问带有标记的节点时才进行。

因此，对于每一个节点，我们需要增加一个`lazy_tag`（存成数组也可以）

问题的核心在于**什么时候启用懒标记**？例如，如果我在使用懒标记标记了区间$[a_1,b_1]$的一次区间修改，现在第二个操作是希望**区间查询**区间$[a_2,b_2]$的信息，如果这两个区间**有重合**，单纯的store懒标记会出现计算错误。

问题出在哪？**子节点上**，我们假设区间$[a_1,b_1]$恰恰好可以被一个上游节点管辖，而如果我们需要**深入其子节点的时候**，发现**其子节点并没有进行懒标记**，这就是问题所在！我们当然可以向下更新懒标记的内容，但是**这就不是“懒”标记的精髓所在了**，因为最终会下降到管辖单个元素的叶节点，复杂度甚至不如做N次单点修改。

怎么样让其变得更懒？我们可以成为DDL战士！换句话说，**我们可以非必要不更新**，只在需要查询对应区间信息的时候做出更新就可以了。

举一个例子：（OI wiki）

![lazy tag](https://oi-wiki.org/ds/images/segt3.svg)

$t$数组代表的是**懒标记**，此时对于编号为3的节点出现懒标记，但是其子节点没有更新，没关系，还没到ddl。

> 在做了懒标记之后，就不用向下更新了，但是**做懒标记的节点本身是需要更新的**。（懒标记是对孩子节点而言的）

现在我需要查询$[4,4]$区间的值，即找到了6号节点，发现此时需要**更新信息！**换句话说，这个时候**上游节点的标记被清空并且下放**。

因此我们可以给出区间查询的代码：

```cpp
void modify(int node, int l, int r, int val) {
        auto &cur = seg_arr[node];
        if (cur.left >= l && cur.right <= r) {
            cur.data += val;
            cur.lazy_tag += val;
            return;
        }

        // no return? It means it will search the child, push down before searching
        push_down(node);
        auto mid = (cur.left + cur.right) >> 1;
        if (l <= mid) {
            modify(cur.lson, l, r, val);
        }
        if (r > mid) {
            modify(cur.rson, l, r, val);
        }
        push_up(node);
    }
```

- 如果被包裹住，直接返回
- 如果没有被包裹住，说明需要利用**孩子的信息！**此次需要先下传标记，但因为这里是递归实现，只需要下传一次标记就可以。
- 然后对应的递归修改左右孩子。

## All Codes

这样，我们就实现了**线段树的所有模版操作**，完整代码如下：

```cpp
#include <cmath>
#include <cstring>
#include <iostream>
#include <limits>

const int MAXN = 10000;
int arr[MAXN];
class segmentTree {
    struct TreeNode {
        int left, right;
        int lson, rson;
        int lazy_tag;
        long long data;

        TreeNode(int left_ = 0, int right_ = 0, int lson_ = 0, int rson_ = 0, long long data_ = 0) : left(left_), right(right_), lson(lson_), rson(rson_), data(data_) {}
    };

private:
    TreeNode seg_arr[4 * MAXN];

public:
    segmentTree() {
        // build the initial tree (all empty)
        std::memset(seg_arr, 0, sizeof(seg_arr));
    }

    /**
     * @brief Build the initial tree
     * 
     * @param number The index of the tree (array: seg_arr)
     * @param s the left index
     * @param t the right index
     */
    void build(int number, int s, int t) {
        // build the tree
        seg_arr[number].left = s;
        seg_arr[number].right = t;
        seg_arr[number].lazy_tag = 0;
        seg_arr[number].lson = number << 1;
        seg_arr[number].rson = seg_arr[number].lson + 1;


        if (s == t) {
            // finish the end, return
            seg_arr[number].data = arr[s];
            return;
        }

        // not finish, go deeper
        int mid = (s + t) / 2;
        build(seg_arr[number].lson, s, mid);
        build(seg_arr[number].rson, mid + 1, t);

        // update the value of the father node
        push_up(number);
    }

    /**
     * @brief update the value of the father node (Traceback the two son nodes)
     * 
     * @param node father node index
     */
    void push_up(int node) {
        auto &cur = seg_arr[node];
        cur.data = std::max(seg_arr[cur.lson].data,
                            seg_arr[cur.rson].data);
    }

    /**
     * @brief Ask fot the maximum value of [l, r]
     * 
     * @param node (begin with index 1), the seg_arr index
     * @param l the left index
     * @param r the right index
     * @return int 
     */
    int query(int node, int l, int r) {
        auto &cur = seg_arr[node];
        if (l <= cur.left && cur.right <= r) {
            // be totally covered, just return as the end of the recursion
            return cur.data;
        }

        // the son will be get, thus update the lazy tag
        push_down(node);

        auto mid = (cur.left + cur.right) >> 1;
        int res = std::numeric_limits<int>::min();
        if (l <= mid) {
            res = std::max(res, query(cur.lson, l, r));
        }
        if (r > mid) {
            res = std::max(res, query(cur.rson, l, r));
        }
        return res;
    }

    /**
     * @brief Update single value, be banned (it does not use lazy tag)
     * 
     * @param node 
     * @param pos 
     * @param val 
     */
    void updateSingle(int node, int pos, int val) {
        auto &cur = seg_arr[node];
        auto mid = (cur.left + cur.right) >> 1;
        if (cur.lson == cur.rson) {
            // find the leaf node, return!
            cur.data = val;
            return;
        }
        if (pos <= mid) {
            updateSingle(cur.lson, pos, val);
        }
        if (pos > mid) {
            updateSingle(cur.rson, pos, val);
        }
        push_up(node);
    }

    /**
     * @brief push down the lazy tag (for only one layer)
     * 
     * @param node 
     */
    void push_down(int node) {
        auto &cur = seg_arr[node];
        if (cur.left == cur.right) {
            // for the leaf node, do not push down
            return;
        }
        if (cur.lazy_tag != 0) {
            seg_arr[cur.lson].data += cur.lazy_tag;// for the maximum value
            seg_arr[cur.rson].data += cur.lazy_tag;
            seg_arr[cur.lson].lazy_tag += cur.lazy_tag;
            seg_arr[cur.rson].lazy_tag += cur.lazy_tag;
            cur.lazy_tag = 0;
        }
    }

    /**
     * @brief Add val for all in [l, r]
     * 
     * @param node begin from index 1
     * @param l 
     * @param r 
     * @param val 
     */
    void modify(int node, int l, int r, int val) {
        auto &cur = seg_arr[node];
        if (cur.left >= l && cur.right <= r) {
            cur.data += val;
            cur.lazy_tag += val;
            return;
        }

        // no return? It means it will search the child, push down before searching (update the lazy tag)
        push_down(node);
        auto mid = (cur.left + cur.right) >> 1;
        if (l <= mid) {
            modify(cur.lson, l, r, val);
        }
        if (r > mid) {
            modify(cur.rson, l, r, val);
        }
        push_up(node);
    }
};


int main() {
    const int n = 10;
    segmentTree segTree;
    for (int i = 1; i <= n; ++i) {
        arr[i] = i;
    }

    segTree.build(1, 1, n);


    std::cout << "Initial maximum in range [1, 5]: " << segTree.query(1, 1, 5) << std::endl;
    std::cout << "Initial maximum in range [1, 3]: " << segTree.query(1, 1, 3) << std::endl;


    segTree.modify(1, 2, 4, 10);

    std::cout << "After modifying range [2, 4] by +10:" << std::endl;
    std::cout << "Maximum in range [1, 5]: " << segTree.query(1, 1, 5) << std::endl;
    std::cout << "Maximum in range [3, 3]: " << segTree.query(1, 3, 3) << std::endl;

    segTree.modify(1, 3, 10, 22);
    std::cout << "After modifying range [3, 10] by +22:" << std::endl;
    std::cout << "Maximum in range [1, 5]: " << segTree.query(1, 1, 5) << std::endl;
    std::cout << "Maximum in range [3, 3]: " << segTree.query(1, 3, 3) << std::endl;
    std::cout << "Maximum in range [1, 10]: " << segTree.query(1, 1, 10) << std::endl;
    std::cout << "Maximum in range [6, 10]: " << segTree.query(1, 6, 10) << std::endl;

    segTree.modify(1, 1, 10, 5);
    std::cout << "After modifying range [1, 10] by +5:" << std::endl;
    std::cout << "Maximum in range [1, 10]: " << segTree.query(1, 1, 10) << std::endl;
    std::cout << "Maximum in range [6, 10]: " << segTree.query(1, 6, 10) << std::endl;

    return 0;
}
```

## Examples

线段树的应用就是**区间问题**（区间修改 & 区间查询），因此转化成对一个数组对应的区间问题即可，因此**线段树真正的难点**在于`lazy_tag`的设计和**如何找到合适的数组表示**。来看下面的例题：

### Example 1

已知一个数列，你需要进行下面三种操作：

- 将某区间每一个数乘上 x；
- 将某区间每一个数加上 x；
- 求出某区间每一个数的和。

思路：维护两个tag（加和乘，和分块一模一样），然后在必要的时候做更新就可以了。

在取模的时候要注意！C++的取模负数问题。

#### Code

```cpp
#include <cmath>
#include <cstring>
#include <iostream>

const int MAXN = 100002;
long long arr[MAXN];
long long n, q, m;
struct TreeNode {
    int left, right;
    int lson, rson;
    long long lazy_tag_plus;
    long long lazy_tag_mul;
    long long data;

    TreeNode(int left_ = 0, int right_ = 0, int lson_ = 0, int rson_ = 0, long long data_ = 0) : left(left_), right(right_), lson(lson_), rson(rson_), data(data_) {
        lazy_tag_mul = 1;
        lazy_tag_plus = 0;
    }
};
TreeNode seg_arr[4 * MAXN];
class segmentTree {

public:
    segmentTree() {
        // build the initial tree (all empty)
        std::memset(seg_arr, 0, sizeof(seg_arr));
    }

    /**
     * @brief Build the initial tree
     * 
     * @param number The index of the tree (array: seg_arr)
     * @param s the left index
     * @param t the right index
     */
    void build(int number, int s, int t) {
        // build the tree
        seg_arr[number].left = s;
        seg_arr[number].right = t;
        seg_arr[number].lazy_tag_plus = 0;
        seg_arr[number].lazy_tag_mul = 1;
        seg_arr[number].lson = number << 1;
        seg_arr[number].rson = seg_arr[number].lson + 1;


        if (s == t) {
            // finish the end, return
            seg_arr[number].data = (arr[s] % m + m) % m;
            return;
        }

        // not finish, go deeper
        int mid = (s + t) / 2;
        build(seg_arr[number].lson, s, mid);
        build(seg_arr[number].rson, mid + 1, t);

        // update the value of the father node
        push_up(number);
    }

    /**
     * @brief update the value of the father node (Traceback the two son nodes)
     * 
     * @param node father node index
     */
    void push_up(int node) {
        auto &cur = seg_arr[node];
        cur.data = (seg_arr[cur.lson].data + seg_arr[cur.rson].data) % m;
    }

    /**
     * @brief Ask fot the maximum value of [l, r]
     * 
     * @param node (begin with index 1), the seg_arr index
     * @param l the left index
     * @param r the right index
     * @return int 
     */
    long long query(int node, int l, int r) {
        auto &cur = seg_arr[node];
        if (l <= cur.left && cur.right <= r) {
            // be totally covered, just return as the end of the recursion
            return (cur.data % m + m) % m;
        }

        // the son will be get, thus update the lazy tag
        push_down(node);

        auto mid = (cur.left + cur.right) >> 1;
        long long res = 0;
        if (l <= mid) {
            res += query(cur.lson, l, r);
            res = (res % m + m) % m;
        }
        if (r > mid) {
            res += query(cur.rson, l, r);
            res = (res % m + m) % m;
        }
        return (res % m + m) % m;
    }


    /**
     * @brief push down the lazy tag (for only one layer)
     * 
     * @param node 
     */
    void push_down(int node) {
        auto &cur = seg_arr[node];
        if (cur.left == cur.right) {
            // for the leaf node, do not push down
            return;
        }
        if (cur.lazy_tag_plus != 0 || cur.lazy_tag_mul != 1) {
            seg_arr[cur.lson].data = (seg_arr[cur.lson].data * cur.lazy_tag_mul % m + cur.lazy_tag_plus * (seg_arr[cur.lson].right - seg_arr[cur.lson].left + 1) % m) % m;
            seg_arr[cur.rson].data = (seg_arr[cur.rson].data * cur.lazy_tag_mul % m + cur.lazy_tag_plus * (seg_arr[cur.rson].right - seg_arr[cur.rson].left + 1) % m) % m;

            seg_arr[cur.lson].lazy_tag_plus = (seg_arr[cur.lson].lazy_tag_plus * cur.lazy_tag_mul % m + cur.lazy_tag_plus) % m;
            seg_arr[cur.lson].lazy_tag_mul = seg_arr[cur.lson].lazy_tag_mul * cur.lazy_tag_mul % m;

            seg_arr[cur.rson].lazy_tag_plus = (seg_arr[cur.rson].lazy_tag_plus * cur.lazy_tag_mul % m + cur.lazy_tag_plus) % m;
            seg_arr[cur.rson].lazy_tag_mul = seg_arr[cur.rson].lazy_tag_mul * cur.lazy_tag_mul % m;

            cur.lazy_tag_plus = 0;
            cur.lazy_tag_mul = 1;
        }
    }

    /**
     * @brief Add val for all in [l, r]
     * 
     * @param node begin from index 1
     * @param l 
     * @param r 
     * @param val 
     */
    void modify_plus(int node, int l, int r, long long val) {
        val = (val % m + m) % m;
        auto &cur = seg_arr[node];
        if (cur.left >= l && cur.right <= r) {
            cur.data = (cur.data + val * (cur.right - cur.left + 1) % m);
            cur.lazy_tag_plus = (cur.lazy_tag_plus + val) % m;
            return;
        }

        // no return? It means it will search the child, push down before searching (update the lazy tag)
        push_down(node);
        auto mid = (cur.left + cur.right) >> 1;
        if (l <= mid) {
            modify_plus(cur.lson, l, r, val);
        }
        if (r > mid) {
            modify_plus(cur.rson, l, r, val);
        }
        push_up(node);
    }

    void modify_mul(int node, int l, int r, long long val) {
        val = (val % m + m) % m;
        auto &cur = seg_arr[node];
        if (cur.left >= l && cur.right <= r) {
            cur.data = cur.data * val % m;
            cur.lazy_tag_mul = cur.lazy_tag_mul * val % m;
            cur.lazy_tag_plus = cur.lazy_tag_plus * val % m;
            return;
        }

        // no return? It means it will search the child, push down before searching (update the lazy tag)
        push_down(node);
        auto mid = (cur.left + cur.right) >> 1;
        if (l <= mid) {
            modify_mul(cur.lson, l, r, val);
        }
        if (r > mid) {
            modify_mul(cur.rson, l, r, val);
        }
        push_up(node);
    }
};


int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(0);
    std::cout.tie(0);
    std::cin >> n >> q >> m;
    for (int i = 1; i <= n; i++) {
        std::cin >> arr[i];
    }

    segmentTree seg;
    seg.build(1, 1, n);
    int op, x, y;
    long long k;
    while (q--) {
        std::cin >> op >> x >> y;
        if (op == 1) {
            std::cin >> k;
            seg.modify_mul(1, x, y, k);
        } else if (op == 2) {
            std::cin >> k;
            seg.modify_plus(1, x, y, k);
        } else {
            std::cout << seg.query(1, x, y) << "\n";
        }
    }
    return 0;
}
```

### Example 2

http://poj.org/problem?id=3667

现有一排房间询问：是否有连续x个空房间；如果有，就将最靠前的连续x个房间填满修改：将任意一段房间清空(可能本来就是空的)

#### 思路分析

这道题就需要我们**转化**为线段树的模版了，首先回答第一个问题：**维护什么数组**？**维护什么信息**？

很显然，我们不可以“所见即所得”，如果我们管辖区间的信息就只有**最长的连续空房间个数**，那这个时候“分治”的思想无法正确的解决问题。相关类似的思路在ST表中出现过，我们需要额外**考虑两端的场景**，因此需要维护：

- `l_max`：当前区间**左端**开始的最长连续空房间数
	- 比如区间[空,空,占,空]，`l_max=2`
- `r_max`：当前区间**右端**开始的最长连续空房间数
	- 同上例，`r_max=1`（最右的1个空）
- `max`：当前区间内的**全局**最长连续空房间数（可能出现在中间）
	- 同上例，`max=2`（左侧的两个空）

这样我们就可以快速写出`push_up()`函数，取两端的最大和**拼起来的值**，然后三者取最大值。

接下来，考虑第二个问题：**维护什么lazy-tag**?

在这道题中涉及的区间问询就是问最大的空房间个数，操作是**批量置满或者置空**。因此只需要维护一个`tag`描述状态：`full\empty\null`。

对于问询，我们是可以秒出答案的，（因为这里每一次都是对整个区间进行问询），但是问题在于**我在后续还有置空的操作**，因此我需要下降到子区间，在子区间中满足：当前节点的管辖区间 $\ge k$，并且孩子节点的管辖区间$\le k$。此时在找到**最贴合的情况，然后再去做置空的操作！**（否则去搜索左儿子）

对于修改操作，涉及把一段区间清空 & 填满的操作，就是不断的递归压缩线段树的区间并且修tag的事情，直接看代码就行~

#### Code

```cpp
#include <cmath>
#include <cstring>
#include <iostream>

const int MAXN = 50005;
int op, X, D;
int left_index;

struct TreeNode {
    int left, right;
    int lson, rson;
    int full_tag;
    int lmax = 0, rmax = 0, max = 0;
};
TreeNode seg_arr[MAXN << 2];

class segmentTree {
public:
    void build(int number, int s, int t) {
        auto &cur = seg_arr[number];
        cur.left = s;
        cur.right = t;
        cur.full_tag = 0;
        // 1 for make all empty, and 2 for make all full
        cur.lmax = cur.rmax = cur.max = t - s + 1;

        if (s == t) {
            cur.max = 1;
            cur.lmax = 1;
            cur.rmax = 1;
            return;
        }
        cur.lson = number << 1;
        cur.rson = (number << 1) | 1;

        int mid = (s + t) >> 1;
        build(cur.lson, s, mid);
        build(cur.rson, mid + 1, t);
        push_up(number);
    }

    void push_up(int node) {
        auto &cur = seg_arr[node];
        auto &ls = seg_arr[cur.lson];
        auto &rs = seg_arr[cur.rson];

        cur.max = std::max(ls.max, std::max(rs.max, rs.lmax + ls.rmax));
        cur.lmax = (ls.lmax == (ls.right - ls.left + 1)) ? ls.lmax + rs.lmax : ls.lmax;
        cur.rmax = (rs.rmax == (rs.right - rs.left + 1)) ? rs.rmax + ls.rmax : rs.rmax;
    }

    /**
     * @brief 
     * 
     * @param node 
     * @param l 
     * @param r 
     * @param k 
     * @return if no enough room, return 0, else, return the index of the empty room 
     */
    int query(int node, int l, int r, int k) {
        auto &cur = seg_arr[node];
        auto &ls = seg_arr[cur.lson];
        auto &rs = seg_arr[cur.rson];
        if (l == r) {
            return cur.max >= k ? l : 0;
        }
        // return 0 if there is no enough room

        push_down(node);

        if (seg_arr[cur.lson].max >= k) {
            return query(cur.lson, ls.left, ls.right, k);
        } else if (ls.rmax + rs.lmax >= k) {
            return ls.right - ls.rmax + 1;
        } else if (rs.max >= k) {
            return query(cur.rson, rs.left, rs.right, k);
        }
        return 0;
    }

    void empty(int node, int l, int r) {
        if(D == 0){
            return;
        }

        auto &cur = seg_arr[node];
        if (X <= l && X + D - 1 >= r) {
            // be totally covered
            cur.full_tag = 1;
            cur.lmax = cur.rmax = cur.max = cur.right - cur.left + 1;
            return;
        }

        push_down(node);
        int mid = (l + r) >> 1;
        if (X <= mid) empty(cur.lson, l, mid);
        if (mid < X + D -1) empty(cur.rson, mid + 1, r);
        push_up(node);
    }

    void full(int node, int l, int r) {
        if (D == 0) return;
        auto &cur = seg_arr[node];
        if (X <= l && X + D - 1 >= r) {
            // be totally covered
            // make it all full
            cur.full_tag = 2;
            cur.lmax = cur.rmax = cur.max = 0;
            return;
        }

        push_down(node);
        int mid = (l + r) >> 1;
        if (X <= mid) full(cur.lson, l, mid);
        if (mid < X + D -1) full(cur.rson, mid + 1, r);
        push_up(node);
    }

    void push_down(int node) {
        auto &cur = seg_arr[node];
        if (cur.full_tag == 0 || cur.left == cur.right) return;

        auto &ls = seg_arr[cur.lson];
        auto &rs = seg_arr[cur.rson];

        if (cur.full_tag == 1) {
            // we need to make it all empty
            cur.full_tag = 0;
            ls.full_tag = 1;
            rs.full_tag = 1;
            ls.max = ls.rmax = ls.lmax = ls.right - ls.left + 1;
            rs.max = rs.rmax = rs.lmax = rs.right - rs.left + 1;
        }

        else if (cur.full_tag == 2) {
            // we need to make it all full
            cur.full_tag = 0;
            ls.full_tag = 2;
            rs.full_tag = 2;
            ls.max = ls.rmax = ls.lmax = 0;
            rs.max = rs.rmax = rs.lmax = 0;
        }
    }
};

int main() {
    int N, M;
    std::cin >> N >> M;
    std::ios::sync_with_stdio(false);
    std::cin.tie(0);
    std::cout.tie(0);

    segmentTree sg;
    sg.build(1, 1, N);

    while (M--) {
        std::cin >> op;
        if (op == 1) {
            std::cin >> D;
            left_index = sg.query(1, 1, N, D);
            if(left_index != 0){
                X = left_index;
                // the segement needs to be filled is [left_index, left_index + D - 1];
                sg.full(1,1,N);
            }
            std::cout << left_index << "\n";
        } else {
            std::cin >> X >> D;
            sg.empty(1, 1,N);
        }
    }
    return 0;
}
```

### Example 3

题目：[区间问询平均数和方差](https://acm.sjtu.edu.cn/OnlineJudge/problem/1119)

大概题目就是：给定数组$a[1,n]$，操作为区间加。对于每一次问询，需要输出其**区间内的平均数**或者**方差**。

思路很简单，就是维护区间和和区间的平方和，注意tag的维护顺序。

#### Code

```cpp
#include <cmath>
#include <cstring>
#include <iostream>

const int MAXN = 100002;
double arr[MAXN];

struct TreeNode {
    int left, right;
    int lson, rson;
    double sum_1;
    double sum_2;//平方和
    double lazy_tag;
};
TreeNode seg_arr[4 * MAXN];

class segmentTree {
public:
    /**
     * @brief Build the initial tree
     * 
     * @param number The index of the tree (array: seg_arr)
     * @param s the left index
     * @param t the right index
     */
    void build(int number, int s, int t) {
        auto &cur = seg_arr[number];
        cur.left = s;
        cur.right = t;
        cur.lson = number << 1;
        cur.rson = (number << 1) + 1;

        // lazy add
        cur.lazy_tag = 0;


        if (s == t) {
            // finish the end, return
            cur.sum_1 = arr[s];
            cur.sum_2 = arr[s] * arr[s];
            return;
        }

        // not finish, go deeper
        int mid = (s + t) / 2;
        build(cur.lson, s, mid);
        build(cur.rson, mid + 1, t);

        // update the value of the father node
        push_up(number);
    }

    /**
     * @brief update the value of the father node (Traceback the two son nodes)
     * 
     * @param node father node index
     */
    void push_up(int node) {
        // update sum1 and sum2
        auto &cur = seg_arr[node];
        auto &ls = seg_arr[cur.lson];
        auto &rs = seg_arr[cur.rson];

        cur.sum_1 = ls.sum_1 + rs.sum_1;
        cur.sum_2 = ls.sum_2 + rs.sum_2;
    }

    double query_sum(int node, int l, int r) {
        auto &cur = seg_arr[node];
        if (l <= cur.left && r >= cur.right) {
            // be covered
            return cur.sum_1;
        }

        push_down(node);
        int mid = (cur.left + cur.right) >> 1;
        double ans = 0;
        if (l <= mid) {
            // go to the left
            ans += query_sum(cur.lson, l, r);
        }

        if (r > mid) {
            ans += query_sum(cur.rson, l, r);
        }
        return ans;
    }

    double query_sum_square(int node, int l, int r) {
        auto &cur = seg_arr[node];
        if (l <= cur.left && r >= cur.right) {
            // be covered
            return cur.sum_2;
        }

        push_down(node);
        int mid = (cur.left + cur.right) >> 1;
        double ans = 0;
        if (l <= mid) {
            // go to the left
            ans += query_sum_square(cur.lson, l, r);
        }

        if (r > mid) {
            ans += query_sum_square(cur.rson, l, r);
        }
        return ans;
    }


    /**
     * @brief push down the lazy tag (for only one layer)
     * 
     * @param node 
     */
    void push_down(int node) {
        auto &cur = seg_arr[node];
        auto &ls = seg_arr[cur.lson];
        auto &rs = seg_arr[cur.rson];
        if (cur.left == cur.right) {
            // for the leaf node, do not push down
            return;
        }
        if (cur.lazy_tag != 0) {
            // for sum1, add directly
            ls.sum_2 += 2 * ls.sum_1 * cur.lazy_tag + (ls.right - ls.left + 1) * (cur.lazy_tag) * cur.lazy_tag;
            rs.sum_2 += 2 * rs.sum_1 * cur.lazy_tag + (rs.right - rs.left + 1) * (cur.lazy_tag) * cur.lazy_tag;
            ls.sum_1 += (ls.right - ls.left + 1) * cur.lazy_tag;
            rs.sum_1 += (rs.right - rs.left + 1) * cur.lazy_tag;
            ls.lazy_tag += cur.lazy_tag;
            rs.lazy_tag += cur.lazy_tag;
            cur.lazy_tag = 0;
        }
    }

    /**
     * @brief Add val for all in [l, r]
     * 
     * @param node begin from index 1
     * @param l 
     * @param r 
     * @param val 
     */
    void modify(int node, int l, int r, double val) {
        auto &cur = seg_arr[node];
        if (cur.left >= l && cur.right <= r) {
            // be covered
            cur.sum_2 += 2 * cur.sum_1 * val;
            cur.sum_2 += (cur.right - cur.left + 1) * val * val;
            cur.sum_1 += (cur.right - cur.left + 1) * (val);
            cur.lazy_tag += val;
            return;
        }

        push_down(node);
        auto mid = (cur.left + cur.right) >> 1;
        if (l <= mid) {
            modify(cur.lson, l, r, val);
        }

        if (r > mid) {
            modify(cur.rson, l, r, val);
        }

        push_up(node);
    }
};


int main() {
    // std::ios::sync_with_stdio(false);
    // std::cin.tie(0);
    // std::cout.tie(0);
    int N, M;
    std::cin >> N >> M;
    std::memset(arr, 0, sizeof(arr));
    std::memset(seg_arr, 0, sizeof(seg_arr));

    for (int i = 1; i <= N; i++) {
        std::cin >> arr[i];
    }

    segmentTree sg;
    sg.build(1, 1, N);

    int op, x, y;
    double k;
    double sum = 0;
    double sum2 = 0;
    while (M--) {
        std::cin >> op >> x >> y;
        if (op == 1) {
            std::cin >> k;
            sg.modify(1, x, y, k);
        } else if (op == 2) {
            std::cout << int(sg.query_sum(1, x, y) / (y - x + 1) * 100) << '\n';
        } else {
            sum = sg.query_sum(1, x, y);
            sum2 = sg.query_sum_square(1, x, y);
            int length = (y - x + 1);
            double ret = sum / length;
            std::cout << int((sg.query_sum_square(1, x, y) / (y - x + 1) - ret * ret) * 100) << "\n";
        }
    }
    return 0;
}
```

## Conclusion

线段树的优化核心：**区间预处理和维护信息  &&  懒惰标记的延迟更新**

对于一道问题，首先需要判断他是否是**区间问题**。（区间问题就是对于单个原子操作是区间修改和区间查询，然后需要处理$Q$次原子操作的问题）在判断完之后，如果使用线段树算法，**关键点1**就是确定区间需要维护的信息。（**这个有的时候并不是所见即所得**！！！）例如hotel例题，我们需要额外维护左端极长和右端极长，在方差问题中，我们需要维护区间平方和。（这也是算法分析的难点所在）

在构思好这个问题之后，我们就可以完成**区间查询和单点修改的操作了**。（线段树的阉割版）**关键点2**在于找到什么tag需要被懒加载。（这个tag往往是和所做的区间操作有关），在维护好tag之后，就可以完成`push_down()`函数来实现懒加载。

This is Algorithms!
