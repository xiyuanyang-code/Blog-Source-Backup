---
title: DataStructure-AVL-Tree
date: 2025-04-10 13:22:30
index_img: /img/cover/AVL.jpg
excerpt: AVL Tree and its applications.
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
  - AVL Tree
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# AVL Tree

## Introduction

**Intuition**: 希望保证操作后$O(h)=O(\log n)$。

### 理论证明

证明如下定理：一颗有N个节点的AVL Tree，高度满足$H \le 1.44\log(N + 1) - 0.328$

> 等价问题的转化：**确定高度最小化节点** == **确定节点最大化高度**

设$S_H$为高度为$H$的最小的AVL树（节点最少），显然：

$|S_1| = 1$，$|S_2| = 2$，$|S_3| = 4$， $|S_4| = 7$...

{% note info %}

我们需要在保证为**平衡树（平衡因子）**的情况下尽可能压榨这棵树，使其节点数最少。

因此，我们可以给出如下的递推公式：

$$S_1 = 1, S_2 = 2, S_n = S_{n-1} + S_{n-2} + 1$$

**和斐波那契数列非常像！**记$F_i$为斐波那契数列，很容易证明$S_H = F_{H+2} -1$。

带入斐波那契数列的通项公式，可以得到$S_H$的通项：

$$S_H= [(\frac{1+\sqrt{5}}{2})^{H+2} - (\frac{1-\sqrt{5}}{2})^{H+2}] \times \frac{\sqrt{5}}{5}-1$$

经过一顿放缩（这里省略），最终可以得证。

{% endnote %}

> **$S_n = S_{n-1} + S_{n-2} + 1$**的递推式非常关键，这表明了在AVL Tree中，同样也存在**递归的思想**！

![AVL Tree的递推公式](https://s1.imagehub.cc/images/2025/04/10/d9895bed03c4641620fe15031c22b62b.png)

## 插入

- AVL的插入仍然在**叶节点**，但是相比于二叉查找树，多了一步**调整的操作**，需要保证二叉树的**平衡性**

	- 因此，在插入时，仍然保持**递归的插入方式**，但是多了检查的一步，寻找第一个平衡因子失衡为$\pm 2$的节点（也就是危机节点），然后需要**进行旋转保持树的平衡性**。
	- 在回溯的过程中，需要验证其是否失衡（在插入完成后）

	> 由于**检查&旋转**是在递归终止之后回溯的时候进行的，也就是说顺序是**自底向上**，即我们只需要rotate第一个找到不平衡的节点，也就是危机节点。

![AVLTree](https://s1.imagehub.cc/images/2025/04/10/87929a16c30874070e78cfbcc3f6170c.jpg)

## 删除

对于一般的二叉查找树，AVL树需要找到右子树的**最靠右**的节点，也就是**在中序遍历中紧挨在他后面的节点**。对于AVL树，同样也需要额外的旋转操作，因为删除叶节点同样也可能会导致树的失衡。

首先，我们对要被删除的结点$x$做分类讨论：

- $x$为叶节点
- $x$有一个儿子
- $x$有两个儿子

> 前两种情况非常简单，**不会产生非平衡的情况**。

完成和二叉查找树类似的删除操作之后，有可能会带来**从$x$的父节点到根节点之间的祖先节点的平衡度发生变化**，导致失衡。因此，在回溯阶段，需要向上检查，并做相应的旋转操作。但是删除操作比插入操作更加复杂，有可能需要旋转多个节点。因此，我们当检查在旋转调整后对应节点的高度保持不变时，**即代表更新完毕**，整棵树已经达到平衡的状态。

不失一般性，**我们认为删除发生在左子树**，一般会出现以下这5个状态。（**注意是删除后的状态，因为检查环节在回溯部分**）

![删除的若干种情况](https://s1.imagehub.cc/images/2025/04/10/bd8b80c3297f7f04f120ba88600d7a6b.png)

- 对于情况1，树的高度并没有发生变化，并且树的平衡性不变。**返回true，不用再对祖先节点做操作**。
- 对于情况2，树的高度从$h + 1$降低到$h$，但是**子树的平衡性**仍然保持，此时需要返回False。
- 对于情况3，很遗憾，两颗子树本来是$h-1$和$h$，同时删除操作发生在高度为$h-1$的树上变成了$h-2$，此时需要RR旋转一次，满足平衡性。同时树的高度更新为$h-1$，继续向上返回false。
- 对于情况4，**使用RL旋转**，但是此时子树的高度变矮了一层，需要向上返回False
- 对于情况5，**使用RR旋转**，并且树的高度保持不变，返回True。

![AVL树的删除](https://s1.imagehub.cc/images/2025/04/10/a2901fde43c3d3a1657e514762b78d08.jpg)

## 代码实现

```cpp
/*
The implementation of AVL Tree, letting the tree maintain balanced
*/

#include <cmath>
#include <iostream>

template<class KEY, class OTHER>
struct set {
    KEY key;
    OTHER other;
};


// Dynamic Search Table
template<class Key, class Other>
class dynamicSearchTable {
public:
    // finding elements of x
    virtual set<Key, Other> *find(const Key &x) const = 0;

    // insert element, maintaining the order
    virtual void insert(const set<Key, Other> &x) = 0;

    // remove an element, maintaining the order
    virtual void remove(const Key &x) = 0;

    virtual ~dynamicSearchTable() {}
};


template<class KEY, class OTHER>
class AVLTree : public dynamicSearchTable<KEY, OTHER> {
    struct AVLNode {
        set<KEY, OTHER> data;
        AVLNode *left;
        AVLNode *right;

        // The height of the node,letting the Tree remain balanced
        int height;

        AVLNode(const set<KEY, OTHER> &element, AVLNode *left_, AVLNode *right_, int h = 1)
            : data(element), left(left_), right(right_), height(h) {}
    };

private:
    AVLNode *root;

private:
    // Tool functions

    /**
         * @brief Update the height of the node
         * 
         * @param root 
         */
    void updateHeight(AVLNode *&root_) {
        if (root_ == nullptr) {
            return;
        } else {
            root_->height = std::max(height(root_->left), height(root_->right)) + 1;
        }
    }

    /**
         * @brief insert the new element in the tree, adjusting structures when necessary
         * 
         * @param x 
         * @param root_ 
         */
    void insert(const set<KEY, OTHER> &x, AVLNode *&root_) {
        if (root_ == nullptr) {
            // insert in an empty tree
            root_ = new AVLNode(x, nullptr, nullptr);
        } else if (x.key < root_->data.key) {
            // insert in the left tree
            insert(x, root_->left);

            // judging whether is is out of balanced
            if (height(root_->left) - height(root_->right) == 2) {
                // LL rotations or LR rotations
                if (x.key < root_->left->data.key) {
                    LL(root_);
                } else {
                    LR(root_);
                }
            }
        } else if (x.key > root_->data.key) {
            // insert in the right tree
            insert(x, root_->right);

            // judging whether it is out of balanced
            if (height(root_->right) - height(root_->left) == 2) {
                // RR rotations or RL rotations
                if (x.key > (root_->right->data).key) {
                    RR(root_);
                } else {
                    RL(root_);
                }
            }
        }
        // Repetetion makes no operations

        // Update the height properties
        updateHeight(root_);
    }

    /**
         * @brief Remove the element with the value x
         * 
         * @param x key value
         * @param root_ current root node
         * @return true if the tree is balanced after the deletion
         * @return false 
         */
    bool remove(const KEY &x, AVLNode *&root_) {
        if (root_ == nullptr) {
            return true;
        }

        if (x == (root_->data).key) {
            // delete the root node
            if (root_->left == nullptr || root_->right == nullptr) {
                // The delete node has at most one child (just swap the position)
                AVLNode *oldNode = root_;

                if (root_->left == nullptr) {
                    root_ = root_->right;
                } else {
                    root_ = root_->left;
                }

                delete oldNode;
                return false;
            } else {
                // The deleted Node has two child node
                AVLNode *tmp = root_->right;
                // Go to the lead node
                while (tmp->left != nullptr) {
                    tmp = tmp->left;
                }

                root_->data = tmp->data;
                // then remove the leaf node (tmp)
                if (remove(tmp->data.key, root_->right)) {
                    return true;
                }

                return adjust(root_, 1);
            }
        }

        if (x < (root_->data).key) {
            // delete on the left subtree
            if (remove(x, root_->left)) {
                return true;
            } else {
                return adjust(root_, 0);
            }
        } else {
            // delete on the right subtree
            if (remove(x, root_->right)) {
                return true;
            } else {
                return adjust(root_, 1);
            }
        }
    }

    /**
         * @brief Make the AVL tree empty
         * 
         * @param root_ current root node
         */
    void makeEmpty(AVLNode *root_) {
        if (root_ == nullptr) {
            return;
        }

        makeEmpty(root_->left);
        makeEmpty(root_->right);
        delete root_;
    }

    /**
         * @brief Return the height of the tree
         * 
         * @param root_ 
         * @return int 
         */
    int height(AVLNode *root_) const {
        if (root_ == nullptr) {
            return 0;
        } else {
            return root_->height;
        }
    }

    // Several Rotations
    /**
         * @brief Rotate if the insertion happens on the left child of the left child
         * 
         * @param danger danger node
         */
    void LL(AVLNode *&danger) {
        AVLNode *t1 = danger->left;

        // rotate to adjust the structure
        danger->left = t1->right;
        t1->right = danger;

        // update height
        updateHeight(danger);
        updateHeight(t1);
    }

    /**
         * @brief Rotation if the insertion happens in the right child of the right child of the danger node
         * 
         * @param danger The danger node
         */
    void RR(AVLNode *&danger) {
        // The same with LL rotations
        AVLNode *t1 = danger->right;

        // rotate RR
        danger->right = t1->left;
        t1->left = danger;

        // update height
        updateHeight(danger);
        updateHeight(t1);
    }

    /**
         * @brief danger.leftchild.rightchild = insertion_place
         * 
         * @param danger 
         */
    void LR(AVLNode *&danger) {
        RR(danger->left);
        LL(danger);
    }

    /**
         * @brief danger.rightchild.leftchild = insertion_place
         * 
         * @param root_ 
         */
    void RL(AVLNode *&danger) {
        LL(danger->right);
        RR(danger);
    }

    /**
         * @brief Judge whether the tree remain the same height after the deletion
         * 
         * @param root_ the current root node
         * @param subTree 1 for deleting on the right child, 0 for left child
         * @return true 
         * @return false 
         */
    bool adjust(AVLNode *&root_, int subTree) {
        if (subTree) {
            // Delete on the right child
            if (height(root_->left) - height(root_->right) == 1) {
                // Case1: the height remains unchanged
                return true;
            } else if (height(root_->left) == height(root_->right)) {
                --(root_->height);
                return false;
            } else if (height((root_->left)->right) > height((root_->left)->left)) {
                LR(root_);
                // The height changed!
                return false;
            } else {
                LL(root_);
                if (height(root_->right) == height(root_->left)) {
                    return false;
                } else {
                    return true;
                }
            }
        } else {
            if (height(root_->right) - height(root_->left) == 1) {
                // Case1: the height remains unchanged
                return true;
            } else if (height(root_->left) == height(root_->right)) {
                --(root_->height);
                return false;
            } else if (height((root_->right)->left) > height((root_->right)->right)) {
                RL(root_);
                // The height changed!
                return false;
            } else {
                RR(root_);
                if (height(root_->right) == height(root_->left)) {
                    return false;
                } else {
                    return true;
                }
            }
        }
    }

public:
    /**
         * @brief Construct a new AVLTree object
         * 
         */
    AVLTree() {
        root = nullptr;
    }

    // destructor
    ~AVLTree() {
        makeEmpty(root);
    }

    /**
         * @brief Finding the elements with the key value of x, the same with BST
         * 
         * @param x 
         * @return set<KEY, OTHER>* 
         */
    set<KEY, OTHER> *find(const KEY &x) const {
        AVLNode *t = root;
        while (t != nullptr && t->data.key != x) {
            if (t->data.key > x) {
                // skip to the left tree
                t = t->left;
            } else {
                t = t->right;
            }
        }

        if (t == nullptr) {
            return nullptr;
        } else {
            return (set<KEY, OTHER> *) t;
        }
    }

    void insert(const set<KEY, OTHER> &x) {
        insert(x, root);
    }

    void remove(const KEY &x) {
        remove(x, root);
    }
};


int main() {
    // Create an AVLTree instance
    AVLTree<int, std::string> tree;

    // Insert elements into the AVL tree
    std::cout << "Inserting elements into the AVL tree..." << std::endl;
    tree.insert({10, "Ten"});
    tree.insert({20, "Twenty"});
    tree.insert({30, "Thirty"});
    tree.insert({40, "Forty"});
    tree.insert({50, "Fifty"});
    tree.insert({25, "Twenty-Five"});

    // Search for an element in the AVL tree
    std::cout << "Searching for key 20 in the AVL tree..." << std::endl;
    auto result = tree.find(20);
    if (result != nullptr) {
        std::cout << "Found: " << result->key << " -> " << result->other << std::endl;
    } else {
        std::cout << "Key 20 not found in the AVL tree." << std::endl;
    }

    // Remove an element from the AVL tree
    std::cout << "Removing key 20 from the AVL tree..." << std::endl;
    tree.remove(20);

    // Search for the removed element
    std::cout << "Searching for key 20 after removal..." << std::endl;
    result = tree.find(20);
    if (result != nullptr) {
        std::cout << "Found: " << result->key << " -> " << result->other << std::endl;
    } else {
        std::cout << "Key 20 not found in the AVL tree." << std::endl;
    }

    // Insert more elements to test balancing
    std::cout << "Inserting more elements to test balancing..." << std::endl;
    tree.insert({5, "Five"});
    tree.insert({15, "Fifteen"});

    // Final search to verify tree structure
    std::cout << "Searching for key 15 in the AVL tree..." << std::endl;
    result = tree.find(15);
    if (result != nullptr) {
        std::cout << "Found: " << result->key << " -> " << result->other << std::endl;
    } else {
        std::cout << "Key 15 not found in the AVL tree." << std::endl;
    }

    std::cout << "AVL tree test completed." << std::endl;

    return 0;
}
```

