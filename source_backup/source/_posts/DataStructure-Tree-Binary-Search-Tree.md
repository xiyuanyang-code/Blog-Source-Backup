---
title: DataStructure-Tree-Binary-Search-Tree-AVL-Tree
date: 2025-03-29 22:06:48
index_img: /img/cover/AVL.jpg
excerpt: Cpp implementation for AVL Tree and Binary Search Tree
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

# Binary Search Tree

## Introduction

**动态查找表**：支持元素的增添、删除操作的集合。

在动态查找表中涉及**元素频繁的插入和删除操作**，因此使用顺序存储是不合适的。在此处，可以使用**二叉树**，在保证结构性和有序性的同时，将对应的时间操作降低到对数级别。**用于处理动态查找表**的树称为查找树，另一种动态查找表的实现是**散列表**，用于集合的数据结构（哈希）。

**二叉查找树**：就是**二分查找的树实现**！

**Definition**：

- **若左子树不空，则左子树的所有元素的键值严格小于根节点**

- **若右子树不空，则右子树的所有元素的键值严格大于根节点**
- **Define it recursively !**

## 代码实现

- **插入操作**：插入的操作在本质上也是一个递归操作

	- 实现需要**查找**操作，因为要保证键值的唯一性
	- 在插入新元素的同时是**插入在叶节点的位置**，这样能够保证BST的有序性。

- **删除操作**

	删除操作相对来说比较复杂，因为删除涉及到**树的分裂**，我们可以从特殊情况说起。

	- 首先，我们还是需要进行查找操作（递归），如果没有找到，那么直接return。
	- 如果找到了对应的**节点**，此时分类讨论：
		- 如果节点为叶节点，即没有孩子，则**直接delete即可**。
		- 如果节点只有一个孩子，也是比较简单的，直接让**孩子节点上移**替代对应的节点即可。
		- **如果节点有两个孩子**，请看下面的分析。

回过头来，**删除操作**需要保证维护**二叉查找树**的结构，什么是二叉查找树的结构，可以从定义出发，同时，我们可以给出第二种等价定义：**二叉树的中序遍历必须保证是有序的**！

{% note primary %}

**这是二叉查找树**的核心。

{% endnote %}

也就是说，只要我们保证中序遍历的有序性，我们更希望保证**删除的节点是叶节点**，因此，我们可以找到对应节点的下一个节点，而这个节点一定是其右子树的中序遍历的第一个节点！

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-03 14:10:59
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-03 15:19:49
 * @FilePath: /Data_structure/single_files/Class_implementation/BST.cpp
 * @Description: Dynamic Search Table
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */

#include <cstdio>
#include <iostream>

template <class KEY, class OTHER>
struct set
{
    KEY key;
    OTHER other;
};


// Dynamic Search Table
template <class Key, class Other>
class dynamicSearchTable {
    public:
        // finding elements of x
        virtual set<Key, Other>* find(const Key& x) const = 0;

        // insert element, maintaining the order
        virtual void insert(const set<Key, Other> &x) = 0;

        // remove an element, maintaining the order
        virtual void remove(const Key &x) = 0;

        virtual ~dynamicSearchTable() {}
};

template <class Key, class Other>
class BST: public dynamicSearchTable<Key, Other> {
    private:
        struct BinaryNode{
            set <Key, Other> data;
            BinaryNode* left;
            BinaryNode* right;

            BinaryNode(const set<Key, Other>& data_, BinaryNode* left_ = nullptr, BinaryNode* right_ = nullptr): data(data_), left(left_), right(right_) {}
        };

        // root node
        BinaryNode* root;


    private:
    // tool function

        /**
         * @brief insert the struct into the binary tree
         * 
         * @param x 
         * @param t the root node (for recursion)
         */
        void insert(const set<Key, Other>&x, BinaryNode*& t) {
            if(t == nullptr){
                // Adding a new element on the leaf node
                t = new BinaryNode(x, nullptr, nullptr);
            }
            else if (x.key < t -> data.key){
                insert(x, t -> left);
            }
            else if (x.key > t -> data.key){
                insert(x, t -> right);
            }
            // If x.key = t -> data.key, won't do anything
        }

        /**
         * @brief remove element with the key value of x
         * 
         * @param x 
         * @param t the root node
         */
        void remove(const Key& x, BinaryNode*& t) {
            // if nullptr, return and end the recursion
            if(t == nullptr){
                return ;
            }

            if(x < t -> data.key){
                remove(x, t -> left);
            }
            else if (x > (t -> data.key)){
                remove(x, t -> right);
            }
            // If we find the element...
            // If it has one child:just replace it with is child
            // If it has zero child: just delete it
            else {
                if(t -> left != nullptr && t -> right != nullptr){
                    BinaryNode *tmp = t -> right;
                    while(tmp -> left != nullptr){
                        //traverse the next element in the traversal order
                        tmp = tmp -> left;
                    }
                    t -> data = tmp -> data;
                    remove(t -> data.key, t-> right);
                }else{
                    BinaryNode* oldnode = t;
                    // If it has one child, replace it
                    t = (t -> left != nullptr) ? t -> left : t -> right;
                    delete oldnode;
                }
            }
        }

        /**
         * @brief Make the tree empty
         * 
         * @param t the root node
         */
        void makeEmpty(BinaryNode* t) {
            if(t == nullptr){
                return;
            }

            makeEmpty(t -> left);
            makeEmpty(t -> right);
            delete t;
        }

        /**
         * @brief find the specific element using the recursion
         * 
         * @param x key value
         * @param t root node
         * @return set<Key, Other>* 
         */
        set<Key, Other>* find(const Key& x, BinaryNode* t) const {
            if(t == nullptr){
                // goto the end (leaf node)
                return nullptr;
            }

            // finding the element successfully
            if(t -> data.key == x){
                // successfully
                return (set<Key, Other>*)t;
            }

            // recursively searching for the left & right subtree
            if(x < t -> data.key) {
                return find(x, t -> left);
            } else {
                return find(x, t -> right);
            }
        }


    public:
        BST() {
            // create an empty tree
            root = nullptr;
        }

        ~BST() {
            makeEmpty(root);
        }

        /**
         * @brief Insert value x in the BST
         * 
         * @param x 
         */
        void insert(const set<Key, Other> &x) {
            insert(x, root);
        }

        /**
         * @brief remove the value with the key value of x
         * 
         */
        void remove(const Key& x) {
            remove(x, root);
        }

        /**
         * @brief find the element with the key value of x
         * 
         * @param x 
         * @return set<Key, Other>* 
         */
        set<Key, Other>* find(const Key& x) const {
            return find(x, root);
        }
};




int main() {
    // 创建一个BST实例
    BST<int, std::string> bst;

    // 插入一些元素
    set<int, std::string> elements[] = {
        {10, "Alice"},
        {5, "Bob"},
        {15, "Charlie"},
        {3, "David"},
        {7, "Eve"},
        {12, "Frank"},
        {18, "Grace"}
    };

    std::cout << "Inserting elements into the BST:" << std::endl;
    for (const auto& elem : elements) {
        bst.insert(elem);
        std::cout << "Inserted: Key = " << elem.key << ", Value = " << elem.other << std::endl;
    }

    // 测试查找功能
    std::cout << "\nTesting find function:" << std::endl;
    int keysToFind[] = {10, 5, 15, 100}; // 100 is not in the tree
    for (int key : keysToFind) {
        set<int, std::string>* result = bst.find(key);
        if (result) {
            std::cout << "Found: Key = " << result->key << ", Value = " << result->other << std::endl;
        } else {
            std::cout << "Key " << key << " not found." << std::endl;
        }
    }

    // 测试删除功能
    std::cout << "\nTesting remove function:" << std::endl;
    int keysToRemove[] = {5, 15, 100}; // 100 is not in the tree
    for (int key : keysToRemove) {
        std::cout << "Removing key " << key << "..." << std::endl;
        bst.remove(key);

        // 验证删除后的查找结果
        set<int, std::string>* result = bst.find(key);
        if (result) {
            std::cout << "Key " << key << " still exists after removal." << std::endl;
        } else {
            std::cout << "Key " << key << " successfully removed." << std::endl;
        }
    }

    // 再次测试查找功能以确认删除效果
    std::cout << "\nRe-testing find function after removals:" << std::endl;
    for (int key : keysToFind) {
        set<int, std::string>* result = bst.find(key);
        if (result) {
            std::cout << "Found: Key = " << result->key << ", Value = " << result->other << std::endl;
        } else {
            std::cout << "Key " << key << " not found." << std::endl;
        }
    }

    return 0;
}
```

## 时间性能

二叉树并不能保证树的平衡，也就是说存在**退化为单链表**的情况。对于最优的情况，时间复杂度是$O(\log n)$，对于**平均情况**，时间复杂度是其$1.38$倍。
