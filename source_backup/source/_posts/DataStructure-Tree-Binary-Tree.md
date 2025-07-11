---
title: DataStructure-Tree-Binary-Tree
date: 2025-02-06 22:35:50
index_img: /img/cover/binarytree.png
excerpt: This Blog introduces several basic knowledge of binary Tree, including the linked implementation and its applications of calculating expressions and using Huffman Tree to minimize loss while coding.
mermaid: true
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Tree
  - Binary Tree
  - Data Structure
  - Finished
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Data Structure: Binary Tree

## Abstract

## Definition

树的定义有两种形式：**递归形式** 和 **延伸形式**。同理，我们对**二叉树**也可以有两种定义的解释：

- **延伸形式**：每一个节点至多延伸出两个子节点。（可以无节点，一个节点或者两个节点）

- **递归形式**：或者为空，或者由**一个根节点和两颗互不相交的子树构成**，而左右子树都是**二叉树**。

> 可以把“二叉树为空”理解为递归调用中的函数终止条件。

根据上面的定义，我们可以归纳二叉树的**五种基本形态**：

| 空二叉树 | 只有根的二叉树 | 只有非空左子树 | 只有非空右子树 | 左右子树均非空 |
| ---- | ------- | ------- | ------- | ------- |

![five categories](https://s1.imagehub.cc/images/2025/02/07/b3ab2a0443e525aee7eb1fbdca255773.png)

{% note warning %}

**注意！** 二叉树是**有序树**，其左右子树存在差异，必须严格区分。

换句话说，下面的两棵树不可以看做是同一颗二叉树！

{% endnote %}

{% mermaid %}

graph TD

A[node] --> B[value_1]

A --> C[value_2]

D[node] --> E[value_2]

D --> F[value_1]

{% endmermaid %}

二叉树是一种特殊的树，但在二叉树内部，又有很多**特殊的二叉树**：

| 名词               | 描述                                                                               |
| ---------------- | -------------------------------------------------------------------------------- |
| **满二叉树**         | 每个节点都有 0 或 2 个子节点，且**所有叶节点都在同一层**。                                               |
| **完全二叉树**        | 除最后一层外，其他层都是满的，且最后一层的节点尽量**靠左排列**。<br>也就是说，满二叉树一定是完全二叉树，把完全二叉树的叶节点全部补齐就可以变成满二叉树。 |
| **平衡二叉树**        | 任意节点的左右子树高度差不超过 1，常见实现如 AVL 树、红黑树。                                               |
| **二叉搜索树 (BST)**  | 左子树所有节点的值小于根节点，右子树所有节点的值大于根节点，且左右子树也分别是二叉搜索树。                                    |
| **AVL 树**        | 一种自平衡二叉搜索树，通过旋转操作保持平衡，确保左右子树高度差不超过 1。                                            |
| **红黑树**          | 一种自平衡二叉搜索树，通过颜色标记和旋转操作保持平衡，确保最长路径不超过最短路径的两倍。                                     |
| **线索二叉树**        | 通过利用空指针指向节点的前驱或后继，优化遍历效率。                                                        |
| **堆 (Heap)**     | 一种完全二叉树，分为最大堆（父节点值大于子节点）和最小堆（父节点值小于子节点）。                                         |
| **B 树**          | 一种多路平衡搜索树，常用于数据库和文件系统，节点可以有多个子节点。                                                |
| **B+ 树**         | B 树的变种，所有数据存储在叶节点，内部节点仅存储索引，适合范围查询。                                              |
| **哈夫曼树**         | 一种带权路径长度最短的二叉树，用于数据压缩（如哈夫曼编码）。                                                   |
| **二叉字典树 (Trie)** | 一种多叉树，用于高效存储和检索字符串，每个节点代表一个字符。                                                   |

## Mathematical Properties

![BinaryTree HandNotes](https://s1.imagehub.cc/images/2025/02/07/69c05a5935d390a5750f7806da87c2d6.png)

## Implementation

根据上一篇博客的内容，对于一般的树结构可以有如下的操作：

- `create()` : create a new tree.

- `clear()` : clear all nodes in a tree.

- `isEmpty()` : judge whether a tree is clean.

- `root()` : find the value of the root node.

- `parent(x)` : find the parent value of node `x`.

- `child(x, i)` : find the `i`th child value of node `x`.

- `remove(x, i)` : remove the `i`th child subtree of node `x`.

- `travese()` : traverse all the nodes in the tree.

在二叉树中，**树的运算可以被进一步的细分**：

- **找孩子节点**可以被细分为**找左子节点和右子节点**。

- **删除子树**可以被细分为**删除左子树和删除右子树**。

- **二叉树的遍历**可以被细分为**前序遍历，中序遍历，后序遍历和层次遍历**。

### Traversal for Binary Tree

下面重点解释一下第三点，对于如下的二叉树：

> 由于mermaid流程图的原因，null表示空，没有节点（这样做是为了区分左节点和右节点）。

{% mermaid %}

graph TD
    1((1)) --> 2((2))
    1 --> 3((3))
    2 --> 4((4))
    2 --> 5((5))
    3 --> 6((6))
    3 --> 7((7))
    4 --> 8((8))
    4 --> 9((9))
    5 --> 10((10))
    5 --> 11((11))
    6 --> 12((12))
    6 --> 13((13))
    7 --> 14((14))
    7 --> 15((15))
    8 --> 16((16))
    9 --> 17((17))
    10 --> 18((18))
    11 --> 19((19))
    12 --> 20((20))

    8 --> 21((null))

    9 --> 22((null))

    10 --> 23((null))

    11 --> 24((null))

    12 --> 25((null))

{% endmermaid %}

在标好号之后（见上文的手写笔记性质5，从上到下，从左到右），**二叉树的遍历**就是从1~n遍历一遍，不过由于**节点之间的关系是层次关系比较复杂**，因此可以有很多种不同的遍历方式。

#### Level-Order Traversal (BFS)

一种最显而易见的**遍历方法**就是根据序号的排列**升序**遍历，具体到树而言，可以做如下定义：

**在访问完`k`层的所有节点后，再按照从左到右的次序访问`k + 1`层的节点**。我们把这种遍历的方式叫做**层次遍历**。

> Level order traversal follows breadth-first search to visit/modify every node of the tree. As BFS suggests, the breadth of the tree **takes priority first** and then **moves to depth**. In simple words, we will visit **all the nodes present at the same level** one-by-one **from left to right**, and then it **moves to the next level** to visit all the nodes of that level.[^1]
> 
> **层次遍历**本质上就是一种**广度优先算法**，何为广度？就是相同层数下的每一个节点都要遍历到，才会继续探索更深的层数。

因此，对于上述的二叉树，遍历的顺序就是从1到20，升序遍历。

至于具体实现：可以使用**队列**来存储节点的in和out，**队列的入队顺序正好就是层次遍历的顺序**。对于队尾元素出队后再将**两个子节点（如果有的话）**进队，这样就保证了**只有在同一层的所有节点全部遍历完之后**才回去考虑下一节点。同时我们的入队满足先左后右的顺序，因此便能轻松实现二叉树的层次遍历。

#### Traversal Order for DFS

上面的遍历方法很直观，但有一个致命的缺陷：**树结构的核心是层次关系，或者说是父子关系**，但是层次便利相邻两个节点的关系是**同级别的邻居关系**，例如10->11，两个节点关系并不密切。因此，**我们需要从树的递归定义出发，探索新的遍历方式**。

新的遍历方式要求遍历相邻的两个节点应存在父子关系（对于大部分而言），因此，**我们需要使用递归的方式**，因为树的父子关系的生成就是依靠的递归定义。这与经典的搜索算法：**深度优先搜索**（DFS）不谋而合。

具体的递归方式如何实现？仿照树的递归定义，我们在每一个**递归单元**无非是要遍历完成这三个东西：**左子树，根节点，右子树**。这三者之间的遍历并没有具体的先后顺序要求（当然我们默认左子树先于右子树被遍历）。因此，DFS类的二叉树遍历还可以被细分为如下三种遍历方式：

- **Preorder Traversal** : `root -> left sub_tree -> right sub_tree`

- **Inorder Traversal** : `left sub_tree -> root -> right sub_tree`

- **Postorder Traversal** : `left sub_tree -> right sub_tree -> root`

> 分类的关键就是在于**根节点**在何时被遍历。

##### Preorder Traversal

我们首先仿照树的定义，引出**前序遍历，或者叫先根遍历**（Preorder Traversal）的定义：

```cpp
//Pseudocode for Preorder Traversal
if(isEmpty()){
    //End for recursion: The sub_tree remaining is empty
    no operations here
}else{
    traverse the root node
    Preorder_traverse(left_subtree)
    Preorder_traverse(right_subtree)
}
```

也就是说，对于每一棵树，拆分成**前序遍历左子树——访问根节点——前序遍历右子树**的顺序，直到达到递归终止条件：**树为空**。

这里的底层原理其实就是二叉树的递归定义：

> 二叉树递归的定义方式：**一个树或者为空，或者由一个根节点及两颗互不相交的子树构成。而子树也是二叉树**。

对于上面的树，先根遍历的顺序是：1,2,4,8,16,9,17,5,10,18,11,19,3,6,12,20,13,7,14,15.

> **The essence of Inorder Traversal** is **DFS** !
> 
> The atomic operations in a recursive function are the same as inorder traversal but in a different order. Here, we visit the current node first and then go to the left subtree. After covering every node of the left subtree, we will move toward the right subtree and visit in a similar fashion.

##### Inorder Traversal

中序遍历（中根遍历）：**根节点夹在左右子树之间被遍历**。

```cpp
//Pseudocode for Inorder Traversal
if(isEmpty()){
    //End for recursion: The sub_tree remaining is empty
    no operations here
}else{
    Inorder_traverse(left_subtree)
    traverse the root node
    Inorder_traverse(right_subtree)
}
```

> 值得一提的是，这是一种**最常见的DFS搜索模型**，严格按照从左至右的顺序进行。

对于上面的树，遍历顺序为：16,8,4,17,9,2,18,10,5,19,11,1,20,12,6,13,3,14,7,15.

##### Postorder Traversal

后序遍历（后根遍历）：**根节点在最后遍历**。

```cpp
//Pseudocode for Postorder Traversal
if(isEmpty()){
    //End for recursion: The sub_tree remaining is empty
    no operations here
}else{
    Postorder_traverse(left_subtree)
    Postorder_traverse(right_subtree)
    traverse the root node
}
```

遍历顺序：16,8,17,9,4,18,10,19,11,5,2,20,12,13,6,14,15,7,3,1

##### Conclusion

{% mermaid %}

graph TD
 1((1)) --> 2((2))
 1 --> 3((3))
 2 --> 4((4))
 2 --> 5((5))
 3 --> 6((6))
 3 --> 7((7))
 4 --> 8((8))
 4 --> 9((9))
 5 --> 10((10))
 5 --> 11((11))
 6 --> 12((12))
 6 --> 13((13))
 7 --> 14((14))
 7 --> 15((15))
 8 --> 16((16))
 9 --> 17((17))
 10 --> 18((18))
 11 --> 19((19))
 12 --> 20((20))

    8 --> 21((null))

    9 --> 22((null))

    10 --> 23((null))

    11 --> 24((null))

    12 --> 25((null))

{% endmermaid %}

层次遍历：升序遍历

先根遍历：1,2,4,8,16,9,17,5,10,18,11,19,3,6,12,20,13,7,14,15.

中根遍历：16,8,4,17,9,2,18,10,5,19,11,1,20,12,6,13,3,14,7,15.

后跟遍历：16,8,17,9,4,18,10,19,11,5,2,20,12,13,6,14,15,7,3,1

{% note info %}

**一个很有意思的小问题：在已知这三种遍历顺序的哪几种的情况下**，可以唯一确定一颗二叉树？

> 在这里不考虑层次遍历，因为层次遍历无法提示我们层数的信息。

先给出结论：

| 已知信息             | 结果  |
| ---------------- | --- |
| **已知前序和中序的遍历顺序** | ✅   |
| **已知后序和中序的遍历顺序** | ✅   |
| **已知前序和后序的遍历顺序** | ❌   |

具体的解释比较难讲清楚：关键就在于**确定根节点——左子树——右子树三者之间的关系**，形象的来说，现在每个节点都像一颗珠子，我们需要根据**已知遍历顺序把他们归纳成三堆：左子树，根节点和右子树**。

再对左子树和右子树做相同的递归操作，最后可以画出树形结构。

那为什么最后一种不可以呢？**因为二叉树严格区分左右子树**，但是前序和后序遍历左右子树的挨着的，会出现无法区分的情况，例如：前序AB，后序BA，A为根节点，但无法确定B为左子树还是右子树。

{% endnote %}

### Abstract Class

```cpp
/**
 * @brief Abstract class for Binary Tree
 * 
 * @tparam T 
 */
template <class T>
class BinaryTree{
    public:
        virtual void create() = 0;
        virtual bool isEmpty() const = 0;
        virtual T root(T invalid_flag) const = 0;
        virtual T parent(T current_node, T invalid_flag) const = 0;
        virtual T lchild(T current_node, T invalid_flag) const = 0;
        virtual T rchild(T current_node, T invalid_flag) const = 0;
        virtual void delLeft (T current_node) = 0;
        virtual void delRight (T current_node) = 0;
        virtual void PreOrder() const = 0;
        virtual void InOrder() const = 0;
        virtual void PostOrder() const = 0;
        virtual void LevelOrder() const = 0;
};
```

### Linked Implementation

**二叉树**一般采用链接方式进行存储，因为顺序存储必须为可能存在插入点位的位置腾出空间，**或者说需要先构建一个完全二叉树**，再引入虚节点。但是这样的存储随着层数的上升会带来**几何级数的空间浪费**，因此，绝大部分二叉树采用链接实现。

链表的核心在于**额外存储指针信息来实现遍历（跳转到其他节点）**。具体来说有如下形式：

| 普通链表                         | 二叉树链表                |
| ---------------------------- | -------------------- |
| 单向链表：单向遍历，只需要储存一个指针的值。       | **二叉链表**：只储存两个孩子的地址  |
| 双向链表：可以双向遍历，需要储存前继节点和后继结点的值。 | **三叉链表**：还要储存父亲节点的地址 |
| 循环链表：从链尾回到链头（此处不讨论）          |                      |

![Ternary Linked List](https://s1.imagehub.cc/images/2025/02/08/b9f88b36919b4c1bee78fbd32d7b6b4f.png)
![Binary Linked List](https://s1.imagehub.cc/images/2025/02/08/e36026a7fe5b69d36cfeb2652e711d86.png)

我们把**二叉链表**成为**标准存储结构**，把**三叉链表**称为**广义标准存储结构**。

| 二叉链表     | `left`     | `data`     | `right`     |             |
| -------- | ---------- | ---------- | ----------- | ----------- |
| **三叉链表** | **`data`** | **`left`** | **`paret`** | **`right`** |

为了方便，我们在下文只实现**二叉树的二叉链表类**。

二叉树的其余成员函数实现都相对比较简单。下面给出类的声明和一些重要函数的实现思路。

#### Definition

#### `createTree()`

在这个函数中，我们需要创建**一颗新的树**。来看代码：

```cpp
//创建树
template<class T>
void binaryTree<T>::creatTree(T flag) {
    std::queue<Node *>que;
    Node *tmp;
    T x,l_data,r_data;
    //创建树，输入flag表示空
    std::cout << "Enter the root node";
    std::cin >> x;
    root = new Node(x);
    que.push(root);
    while(!que.empty()) {
        tmp = que.top();
        que.pop();
        std::cout << "Enter" << tmp->data <<"two sons (" << flag << ") represents the null node";
        std::cin >> l_data >> r_data;
        if(l_data != flag){
            que.push(tmp -> left = new Node(l_data));
        }
        if(r_data != flag){
            que.push(tmp->right = new Node(r_data));
        }
    }
}
```

基本实现思路：采用和**层次遍历**相同的实现思路。（使用队列）

{% note primary %}

**二叉树**的实现看似复杂，其实思想非常的简单。只需要在脑海中存在树的图像，并且灵活运用**树的递归定义**即可。

{% endnote %}

#### 二叉树遍历的非递归实现 栈实现

{% note primary %}

递归函数是程序设计中非常经典的一个算法环节，但是传统的函数递归调用是建立在**栈内存**上的，编译器自动为函数递归的层层调用创建**栈帧空间**，存在不小的时间和空间开销（例如函数指针）。因此，我们也可以使用栈自己模拟**二叉树遍历的递归版本**，有关栈模拟递归的讲解在本质上就是**手搓状态机和栈帧空间**，具体的教程可以看如下的材料：

- [栈模拟递归](https://xiyuanyang-code.github.io/posts/DataStructure-Stack-Queue-Advanced/)
- [从状态机的角度看栈模拟递归的实现](https://www.bilibili.com/video/BV1HTAWeTEo3/?share_source=copy_web&vd_source=1722361acea5d9b6aee1f9fef0777c69&t=998)

{% endnote %}

{% note primary %}

1.执行代码块0

2.保存现场准备进入下一层

3.接受下层返回的数据

4.恢复现场

5.继续执行代码块1

{% endnote %}

##### 例题 二叉树的后根遍历的栈模拟递归实现

```cpp
#pragma once
#ifndef SRC_HPP
#define SRC_HPP

/*
The recursive implementation:
traverse(x.left)
traverse(x.right)
traverse(x)

the default function
if(node.left == nullptr && node.right == nullptr){
    traverse the node of x
}


*/

class Node;
struct Info {
  // Add whatever you want here
  int current_size; // now the traversa has gone to which section
  int pc;
  //pc = 1: The initial statement
  //pc = 2: Has been traversed the left node
  //pc = 3: has been traversed the right node
  const Node*  root_node;
  int *arr;
  int total_treesize;
  




  Info(int current= 0, int pc= 1, const Node*  root= nullptr, int *arr =nullptr, int total_treesize = 0)
  :current_size(current), pc(pc),root_node(root), arr(arr),total_treesize(total_treesize) {
    // TODO:
  }

  ~Info() {
    // TODO:
  }
};

class Node {  // DO NOT edit this part
 private:
  int _data;
  Node *_lson, *_rson;
  mutable Info _info;  // custom data

  friend Node *genTree();

 public:
  Node();

  Node(int data);

  ~Node();

  int data() const;
  const Node *const lson() const;
  const Node *const rson() const;
  Info &info() const;
};

void traversePostOrder(const Node *const root, int *arr, int tree_size) {
  int currentsize = 0;
  Info* st = new Info[tree_size];//the simulation of stack
  int top = 0;
  st[top] = Info(0, 1, root, arr, tree_size);

  while(top >= 0){
    auto& top_val = st[top];
    switch(top_val.pc){
        case 1:
            if(top_val.root_node->lson() == nullptr && top_val.root_node->rson() == nullptr){
                //fill in the root node!
                arr[currentsize] = top_val.root_node->data();
                currentsize ++;
                if(currentsize > tree_size){
                    return;
                }
                top --;
            }
            else if(top_val.root_node->lson() == nullptr){
                top_val.pc = 3;
                st[++top] = Info(currentsize, 1, top_val.root_node->rson(), arr, tree_size);
            }else{
                top_val.pc = 2;
                st[++top] = Info(currentsize, 1, top_val.root_node->lson(), arr, tree_size);
            }break;
        case 2:
            if(top_val.root_node->rson() == nullptr){
                arr[currentsize] = top_val.root_node->data();
                currentsize ++;
                if(currentsize > tree_size){
                    return;
                }
                top --;
            }
            else{
                top_val.pc = 3;
                st[++top] = Info(currentsize, 1, top_val.root_node->rson(), arr, tree_size);
            }break;
        case 3:
            arr[currentsize] = top_val.root_node->data();
                currentsize ++;
                if(currentsize > tree_size){
                    return;
                }
                top --;
            break;
    }
  }
}

#endif
```

> 解释：
>
> - 这里使用的是**模拟栈**，就是一个原始数组。
> - 分析递归调用的核心：
> 	- 定义`pc == 1`为初始状态
> 		- 这个时候函数开始执行，首先应该执行if语句，判断是否为叶节点
> 			- 如果是，那么说明递归到达终点，将此时的栈帧弹出（他已经完成了自己的使命）
> 			- 如果不是，就需要完成以下操作：
> 				- 先遍历左子树（达到下一层递归）
> 				- 遍历右子树（达到下一层递归）
> 				- 遍历自身根节点
> 	- 于是我们便很轻松定义出`pc == 2` 和 `pc == 3` 的状态，分别代表已经遍历完成左子树和右子树。

## Applications

### 计算表达式

在学习**栈**的时候，我们学习了**前缀表达式**和**中缀表达式**，根本思想是利用**运算符**的优先级，通过栈来控制弹出顺序，从而达到计算顺序和输入顺序之间的差异。

我们是否可以使用树来完成这一操作？貌似可操作性非常的大！请看下面的**表达式树**：

![Tree for expressions](https://s1.imagehub.cc/images/2025/03/26/c76d32ad239b58520dbc7601d78043f3.png)

这样的书写方式相比于波兰表达式更加的直观。同样的，计算分成两步：

- 将中缀表达式转化成**表达式二叉树**。
- 对二叉树进行**后序遍历**，得到计算结果。
	- 此处还是需要栈来实现运算，如果一个运算符的两个节点全部遍历完成，此时需要完成该运算符的二元运算。

接下来我们来重点研究如何从中缀转成二叉树。

为什么我们可以写成**二叉树**的形式？关键原因在于运算符（所有的二元运算符）可以看成是一个`function`，换句话说，一个表达式的基本结构就是`function(a,b)`，而$a$与$b$都是对应的数，其可以在内部递归调用更小的运算（存在优先级），而这与**二叉树的递归定义**不谋而合！

也就是说，树的构建过程就是树的生长过程，这里看 [这个视频](https://www.bilibili.com/video/BV1Gq4y1i7BX?spm_id_from=333.788.videopod.episodes&vd_source=6955add1d28c52cd48096d58e09ce798&p=22) 比较直观。本质上和中缀转后缀的过程相同！

**总结**：

- 顺序扫描中缀表达式
- 扫描的运算数
	- 运算树为空：作为第一个运算数保存。
	- 运算树非空：作为上一运算符的**右儿子**。
- 如果扫描到 `+` 或 `-`：
	- 在树中，由于我们最后是做的后序遍历，越靠近根节点的运算符越晚运算（相当于根节点的栈帧在最底层，最后被返回），而加减在运算符中的优先级是最低的，并且满足从左至右的顺序。
	- 因此，**需要直接将整颗子树下移，同时将其作为新的根节点**（右子树保持空）
- 如果扫描到 `*` 或 `\`:
	- 与当前的根节点进行比较：
		- 如果根节点也是乘除，那么当前运算符上提为根节点，而原树下移为左子树
		- 如果根节点是加减，则优先级高于他，故下移成为其右子树的根，原先的右子树变成这个节点的左子树。

现在我们加上对**括号**的处理：**括号内部本质上就是调用一个子表达式！**

- 遇到括号，将括号内部的子表达式**构建成一颗子树作为整个表达式的一个运算数**。

#### 代码实现

You can see my Github repo for [Data Structure](https://github.com/xiyuanyang-code/Data_structure/blob/master/single_files/Specific_USage/calculator2.cpp)!

```cpp
/*
Implementatin of expression of Binary Tree
*/
#include <string>
#include <iostream>

class calc{
    enum Type{DATA, ADD, SUB, MULTI, DIV, OPAREN, CPAREN, EOL};

    struct node
    //define the binary tree
    {
        Type type;
        int data;
        node *lchild, *rchild;
        node (Type t, int d = 0, node *lc = nullptr, node *rc = nullptr){
            type = t;
            data = d;
            lchild = lc;
            rchild = rc;
        }
    };
    
    private:
        node* root;
    
        //several tool function
        node* create(const char*& s){
            node *p = nullptr;
            node *root = nullptr;
            Type returntype;
            int value;

            //creating the new tree
            while(*s){
                returntype = gettoken(s, value);
                switch (returntype)
                {
                    case DATA:
                    case OPAREN:
                        if (returntype == DATA) {
                            p = new node(DATA, value);
                        } else {
                            p = create(s);
                            //if is a OPAREN, then we need to create a new subtree recursively!
                        }

                        if (root != nullptr) {
                            if(root -> rchild == nullptr){
                                root -> rchild = p;
                            }else{
                                //it must be * or /
                                root -> rchild -> rchild = p;
                            }
                        }
                        break;
                    case CPAREN:
                    case EOL:
                        // the creation ends
                        return root;
                    case ADD:
                    case SUB:
                        if (root == nullptr){
                            root = new node(returntype, 0, p);
                            //attention, in this case, the data 0 is meaningless for non-data operators;
                        } else {
                            root = new node(returntype, 0, root);
                        }
                        break;
                    case MULTI:
                    case DIV:
                        if(root == nullptr){
                            root = new node(returntype, 0, p);
                        } else if (root -> type == MULTI || root -> type == DIV) {
                            root = new node(returntype, 0, root);
                        } else {
                            root -> rchild = new node(returntype, 0, root -> rchild);
                        }
                }
            }

            //return the root node of the tree
            return root;
        }

        Type gettoken(const char *&s, int &data)
        {
            char type;

            while (*s == ' ') ++s;          // Skip spaces in the infix expression

            if (*s >= '0' && *s <= '9') {   // Encountered a number
                data = 0;
                while (*s >= '0' && *s <= '9') {
                    data = data * 10 + *s - '0';
                    ++s;
                }
                return DATA;
            }

            if (*s == '\0') return EOL;

            type = *s; ++s;
            switch (type) {
                case '+': return ADD;
                case '-': return SUB;
                case '*': return MULTI;
                case '/': return DIV;
                case '(': return OPAREN;
                case ')': return CPAREN;
                default: return EOL;
            }
        }

        int result(calc::node *t)
        {
            int num1, num2;
        
            if (t->type == DATA) return t->data;
            num1 = result(t->lchild);  // Calculate the left sub-expression
            num2 = result(t->rchild);  // Calculate the right sub-expression
            switch (t -> type) {
                case ADD: t->data = num1 + num2; break;
                case SUB: t->data = num1 - num2; break;
                case MULTI: t->data = num1 * num2; break;
                case DIV: t->data = num1 / num2; break;
            }
            return t->data;
        }

    public:
        calc(const char *s){
            root = create(s);
        }
        int result(){
            if(root == nullptr){
                return 0;
            }

            return result(root);
        }
};

int main(){
    std::string exp;
    std::cin >> exp;
    calc cl(exp.c_str());

    std::cout << cl.result() << std::endl;
}
```

### 哈夫曼树和哈夫曼编码

#### Introduction

在文本处理中，我们经常涉及到**字符编码**的问题，例如**ASCII**编码是一种等长的编码，但是在一些情况下为了提高效率，我们往往采用**不等长编码**，使**频率较高的字符具有较短的编码而频率较低的字符具有较长的编码**。

因此，在此处介绍**哈夫曼树**和**哈夫曼编码**，来实现最优化的非等长编码问题。

#### 前缀编码

对于等长的编码形式，使用**完全二叉树**来构建01序列即可解决。例如如果使用等长编码的完全二叉树来构建ASCII，需要7个二进制位，也就是$height = 7$的一颗完全二叉树（在这里是满二叉树）可以构建成功。

很显然，对于等长编码，**非叶节点的存储空间被浪费了**，我们需要做出优化从而修改成非等长编码，但是在此之前，我们需要确立**编码的前提**：

- 对于前缀编码而言，**字符只放在叶节点**中，换句话说，任何一个编码的子集（根节点）不可以作为一个新的编码，否则在解码的过程中会出现冲突（对于前缀编码而言）。
	- 为什么不可以放在根节点中？如果根节点$A$是一个字符的前缀01编码，其衍生出的根节点是在A的01编码的基础之上**再加一位**，产生冲突。
- 本质上确定**前缀可以被唯一解码**。

#### 哈夫曼算法

确定目标：在给定一组字符集$S$的元素及其使用频率的情况下，使计算出最优化的非等长前缀编码算法，使得**期望编码空间的空间使用**最小。

**思路**：

- **权值大的更靠近树根**
- **权值小的更远离树根**（相对的，我们希望每一个字符的编码长度都尽可能的短）

{% note primary %}

给定一个带有权值的集合 $T = \{ T_1,T_2, T_3,T_4,T_5...T_n\}$，在初始状态下$A = T$，之后执行**n-1**次循环：

- 从A中选择**权值最小和次小**的节点
- 生成新的内部节点$b$，$T_1$，$T_2$是其左右儿子，权值为$\omega_1 + \omega_2$.
- 将新节点放入集合中。

{% endnote %}

![Huffman Tree](https://s1.imagehub.cc/images/2025/03/27/c1ea6598712843be773f6bc075839fd7.png)

####  Implementation

在二叉树的过程中，“**儿子找爸爸**”和“**爸爸找儿子**”的情况都会发生。我们可以使用**二叉树的广义存储结构**来实现，同时，对于静态的编码问题，节点的个数保持不变，因此我们可以使用**数组**来存储每一个节点的值。接下来，我们实现哈夫曼算法的实现代码：

```cpp
#include <string>
#include <iostream>
#include <iomanip> 

template <class T>
class hfTree {
private:
    struct Node {
        T data;
        int weight;
        int parent, left, right;
    };

    Node* elem;
    int length; // Fixed size of the tree

public:
    struct hfcode {
        T data;
        std::string code;
    };

    hfTree(const T* x, const int* w, int size) {
        const int MAXINT = 32767;
        int min_1, min_2; // The lowest and second lowest weights
        int least, second_least;

        // Initialize
        length = 2 * size;
        elem = new Node[length];

        // Initial values for leaf nodes
        for (int i = size; i < length; ++i) {
            elem[i].weight = w[i - size];
            elem[i].data = x[i - size];
            elem[i].parent = elem[i].right = elem[i].left = 0;
        }

        // Merge nodes to build the Huffman tree
        for (int i = size - 1; i > 0; --i) {
            min_1 = min_2 = MAXINT;
            least = second_least = 0;

            // Find the two smallest nodes without parents
            for (int j = i + 1; j < length; j++) {
                if (elem[j].parent == 0) {
                    if (elem[j].weight < min_1) {
                        min_2 = min_1;
                        min_1 = elem[j].weight;
                        second_least = least;
                        least = j;
                    } else if (elem[j].weight < min_2) {
                        min_2 = elem[j].weight;
                        second_least = j;
                    }
                }
            }

            // Update the new internal node
            elem[i].weight = min_1 + min_2;
            elem[i].left = second_least;
            elem[i].right = least;
            elem[i].parent = 0;
            elem[least].parent = i;
            elem[second_least].parent = i;
        }
    }

    // Generate Huffman codes
    void getcode(hfcode result[]) {
        int size = length / 2;
        int cur, cur_parent;

        for (int i = size; i < length; ++i) {
            result[i - size].data = elem[i].data;
            result[i - size].code = "";
            cur_parent = elem[i].parent;
            cur = i;

            while (cur_parent != 0) {
                if (elem[cur_parent].left == cur) {
                    result[i - size].code = "0" + result[i - size].code;
                } else {
                    result[i - size].code = "1" + result[i - size].code;
                }
                cur = cur_parent;
                cur_parent = elem[cur_parent].parent;
            }
        }
    }

    // Visualize the tree structure
    void viewTree() {
        std::cout << "Huffman Tree Structure:\n";
        viewTreeHelper(1, 0); // Start from root (index 1) with depth 0
    }

private:
    // Recursive helper function to print the tree
    void viewTreeHelper(int index, int depth) {
        if (index >= length || elem[index].weight == 0) return;

        // Print indentation based on depth
        std::cout << std::setw(depth * 4) << "";

        // Print node information
        if (index >= length / 2) {
            // Leaf node
            std::cout << "Leaf: " << elem[index].data << " (Weight: " << elem[index].weight << ")\n";
        } else {
            // Internal node
            std::cout << "Node (Weight: " << elem[index].weight << ")\n";
        }

        // Recursively print left and right children
        viewTreeHelper(elem[index].left, depth + 1);
        viewTreeHelper(elem[index].right, depth + 1);
    }

public:
    ~hfTree() {
        delete[] elem;
    }
};

int main() {
    // Test data
    char ch[] = {'H', 'e', 'l', 'd', 'o', 'w', 'm', 'r', 'f', 'k'};
    int w[] = {1, 4, 2, 66, 7, 8, 9, 5, 3, 6};

    hfTree<char> tree(ch, w, 10);
    hfTree<char>::hfcode result[10];

    // Generate and print Huffman codes
    tree.getcode(result);
    std::cout << "Huffman Codes:\n";
    for (int i = 0; i < 10; i++) {
        std::cout << result[i].data << ": " << result[i].code << std::endl;
    }

    // Visualize the Huffman tree
    tree.viewTree();
}

```

## References

[^1]: [4 Types of Tree Traversal Algorithms | Built In](https://builtin.com/software-engineering-perspectives/tree-traversal#:~:text=The%20types%20of%20tree%20traversal,traversal%20and%20level%20order%20traversal.)
