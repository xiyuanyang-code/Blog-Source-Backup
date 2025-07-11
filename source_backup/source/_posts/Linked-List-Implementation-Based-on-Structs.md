---
title: Linked-List-Implementation-Based-on-Structs
date: 2024-12-07 19:55:20
index_img: /img/cover/lianbiao.png
excerpt: This blog starts from scratch to demonstrate how to implement a linked list data structure using structs, covering the basic operations including insertion, deletion, traversal, and searching.
categories:
  - Code
  - Cpp
tags:
  - Finished
  - tutorial
  - Linked list
  - Structs
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Linked List Implementation Based on Structs

封面插图截自 [Hello 算法](https://www.hello-algo.com/)

## Abstract

> 这篇博客从0开始实现了如何**使用结构体完成链表**这一数据结构，并且实现了链表的插入，删除，遍历，查找等基本操作
>
> This blog starts from scratch to demonstrate how to **implement a linked list data structure using structs**, covering the basic operations including insertion, deletion, traversal, and searching.

## Concept

数组经常用于同类型数据的储存，但是数组规模必须在使用前确定（**连续的顺序数据结构**），这样会带来空间的浪费和诸多不利。但**链表（Linked List）**可以实现在增加元素的时候按需动态分配新的内存，实现了一种**非连续存储**。

![数组定义与存储方式](https://www.hello-algo.com/chapter_array_and_linkedlist/array.assets/array_definition.png)

![链表定义与存储方式](https://www.hello-algo.com/chapter_array_and_linkedlist/linked_list.assets/linkedlist_definition.png)

> 以上两图节选自 [Hello 算法：数组和链表](https://www.hello-algo.com/chapter_array_and_linkedlist/array/)

对于数据的存储，最常见的一项操作就是**遍历数据类型中的所有（或者）部分元素**，数组通过指针的**自增操作**实现遍历，但受限于非线性的存储方式，链表无法实现这一点。因此，为了能够达到和数组相同的遍历功能，链表中的每一个元素（或被称为**节点**），都需要一块内存来专门存储**指向下一个元素的节点**。

## Categories

- **单向链表**：即前面介绍的普通链表。单向链表的节点包含值和指向下一节点的引用两项数据。我们将首个节点称为头节点，将最后一个节点称为**尾节点**，尾节点指向**空 `None`**（最后一个节点的节点指针指向空指针） 。
- **环形链表**：如果我们令单向链表的尾节点指向头节点（首尾相接），则得到一个环形链表。在环形链表中，任意节点都可以视作头节点。
  - 环形列表也可以分为双循环链表和单循环链表
- **双向链表**：与单向链表相比，双向链表记录了两个方向的引用。**双向链表的节点定义同时包含指向后继节点（下一个节点）和前驱节点（上一个节点）的引用（指针）**。相较于单向链表，双向链表更具灵活性，可以朝两个方向遍历链表，但相应地也需要占用更多的内存空间。

![Categories](https://www.hello-algo.com/chapter_array_and_linkedlist/linked_list.assets/linkedlist_common_types.png)

图片来源：[4.2  链表 - Hello 算法](https://www.hello-algo.com/chapter_array_and_linkedlist/linked_list/#423)

## Definition and Implementaition

链表的每个节点由两个部分组成：**数据元素本身和指向下一节点的指针。**以下以单链表的相关操作为例：

### Definition

```C++
struct linkNode{
    datatype data;、
    //datatype可以为任何一种数据类型，用来描述数据元素的本身的值
    linkNode* next;
};
```

①`datatype`除了可以是一些**基本数据类型**，例如`int`，`double`等之外，也可以是**自定义的非基本数据类型。**例如，可以定义单链表的数据元素为另一个结构体类型的变量，则`datatype`需要相对应的改变。

```C++
struct Node1{
    int thenum;
    Node1 *next;
};
struct Node2{
    Node1 data;
    Node2 *next;
};

#include <iostream>
using namespace std;
int main(){
    Node1 *linklist1;
    //定义了一个单链表
    Node2 *linklist2;
    return 0;
}
```

②next是**指向下一节点的指针**，因此数据类型就是**指向自身类型（自身结构体）**，这种结构被称为**自引用结构**。

### Operations

对于基本数据结构，我们往往会关注一些基本的“**操作**”，例如插入，修改，删除，排序，访问（遍历）等等。这是数据结构走向算法的具体实现（一个从抽象到具象的过程），同时，由于每一种数据结构实现的底层原理有所不同，其具体的操作的方法和复杂度、优劣势等也各不相同。下文将介绍**链表的创建，插入，访问，删除等基本操作通过结构体实现的底层原理**，并给出一些具体的应用。

链表由于其自身**非连续的物理结构特征**，**比数组占用更多的内存空间（用来存储下一个节点的地址），但是更加的灵活，扩展性强。**如此结构使链表相比于数组具有更快的插入和删除元素的速度，但是牺牲了访问遍历元素的复杂度。

<center>表 4-1  数组与链表的效率对比</center>

|          |              数组            |          链表              |
| :------: | :----------------: | :----------------:  |
| 存储方式  |          连续内存空间          |  分散内存空间               |
| 容量扩展  |           长度不可变           |   可灵活扩展               |
| 内存效率  | 元素占用内存少     | 元素占用内存多             |
| 访问元素  |              O(1)              |      O(n)               |
| 添加元素  |              O(n)              |      O(1)               |
| 删除元素  |              O(n)              |      O(1)                |

选自 [Hello算法：数组和链表的比较](https://www.hello-algo.com/chapter_array_and_linkedlist/linked_list/#422-vs)

接下来，我们一步一步实现自己的链表。

首先定义**节点**，这是每个链表的基本单元，包括`val`（储存当前节点的值）和`next`指向下一个节点地址的指针，在后文笔者习惯把它称为这个节点的**引用**。

注意到在结构体中增加了一行隐式构造函数，可以在新创建一个节点时设置默认参数（避免指针产生许多奇奇怪怪的问题）

```C++
struct ListNode {
    int val;
    ListNode *next;
    ListNode(int value = 0, ListNode *next = nullptr) : val(value), next(next) {}
};
```

#### 单链表的创建

如何初始化一个单链表？难道就是**空空如也？**显然不是，在之后的许多操作时，为了避免指针产生越界的情况，我们经常需要对链表进行**置空检查**（判断链表是否为空）。因此，我们需要再初始化一个新链表时，创建一个**头结点**，并对头节点的指向下一节点的指针初始化为**空指针**。

> 以下对链表的若干操作都写成函数的形式，方便向OOP的过渡。
>
> 我既然都写析构函数和构造函数了，为什么不直接创建一个类呢（乐）

**头结点非常的重要**！！！它代表着链表这一条长蛇的开端，很多操作都是从头结点开始的。

在之后的操作中，`head->next==nullptr`就是对链表的置空检查（可以写成函数封装的形式），如果返回值为`true`，则代表链表为空，需要对头结点直接操作。

**头结点是链表中额外加入的一个特殊结点，它不存放数据，只是作为链表的开始标记，位于链表的最前面，它的指针部分指向表中的第一个元素，这样就保证了链表中每个结点前面都有一个结点 。**

> 其实头结点也可以指向第一个实际数据，但是这样会导致在后续的链表操作中如果涉及对头结点（也就是首元素）的修改，会变得非常复杂，在这里笔者给出头结点不储存数据的链表实现。

我们定义的第一个函数就是结构体的**默认构造函数**，只有在创建一个新链表的时候才会使用到，其功能是将头结点的引用设置为空指针。同样的，我们也可以设置**析构函数**，在链表结束其生命周期的时候自动调用，通过while循环自动`delete`所有节点（包括头结点）。

```C++
struct LinkedList {
    // By default, construct a sentinel node that does not store any value, and its reference points to the first element of the list
    // Initially, it points to nullptr
    ListNode* sentinel;
    int size = 0;
    //size represents the number of nodes in the Linkedlist

    // Constructor: Initialize the sentinel node, allocate memory on the heap for it
    LinkedList(): sentinel(new ListNode()), size(0) {
        cout << "Created successfully" << endl;
    };

    // Destructor: Iteratively delete all nodes (including the memory pointed to by the sentinel node initially)
    // Once the sentinel points to a null pointer, the destruction process is complete
    ~LinkedList() {
        while (sentinel != nullptr) {
            ListNode* temp = sentinel;
            sentinel = sentinel->next;
            delete temp;
        }
        cout << "Destroyed successfully" << endl;
    }
```

#### 单链表的插入

我们先来看一种比较原始的插入方式：

![Insert](Insert.png)

由上图可知，如果要再节点p的后面插入一个新的节点temp，需要完成以下几件事：

- 修改节点p中指向下一个节点的指针，使其指向新节点temp的地址
- 修改节点temp中指向下一个节点的指针，使其指向原来在p后面的节点的地址

> 看到了吗？链表的操作本质上就是对指针的操作！

因此，我们可以给出插入新节点的第一个函数 `insertAfter`，实现在当前节点current后面插入新的节点，节点值是value。

```C++
void insertAfter(ListNode* current, int value) {
        // If the current node is null, return or throw an exception
        if (current == nullptr) {
            throw std::invalid_argument("Current node is null");
            return;
        }

        // Create a new node
        ListNode* newNode = new ListNode{value, current->next};
        // Insert the new node after the current node
        current->next = newNode;
        size++;
    }
```

##### Modify①插入到第一个节点

这个问题非常好解决，相当于更换了链表的头结点，只需要更改插入节点的引用即可

```C++
void insertAtHead(int value) {
        // Set the reference of the new node to the reference of the sentinel node
        ListNode* newNode = new ListNode{value, sentinel->next};
        // Change the reference of the sentinel node because the first element of the list has changed
        sentinel->next = newNode;
        size++;
    }
```

##### Modify②如何更加的高效？（配合查找）

如果我们现在需要插入一个新节点使新节点的索引为3，应该怎么办？那我们需要先遍历节点直到达到目标节点，然后再调用`insertAfter`函数进行目标的插入，当然，如果索引为0，直接`insertAtHead`即可。

```C++
void insertAtPosition(int value, int position) {
        // If the position is 0 or the list is empty, insert at the head
        if (position == 0 || sentinel->next == nullptr) {
            insertAtHead(value);
            return;
        }

        ListNode* current = sentinel->next;
        int currentPosition = 0;
        // Set a pointer to start the traversal

        // Find the node before the insertion position
        while (current->next != nullptr && currentPosition < position - 1) {
            current = current->next;
            currentPosition++;
        }

        // If the position exceeds the end of the list, insert at the end
        if (currentPosition < position - 1) {
            ListNode* newNode = new ListNode{value, nullptr};
            current->next = newNode;
            size++;
        } else {
            // Insert the new node
            // current represents the node at index n-1, inserting after it achieves the desired index
            insertAfter(current, value);
        }
    }
```

##### Modify③直接在尾部插入

这里提供了一种比较方便的插入函数，当然我们在定义结构体时也定义了size来维护结构体中的节点个数，因此也可以通过`insertAtPosition`来实现

```C++
// Insert directly at the end
    void insertAtEnd(int value) {
        if (sentinel->next == nullptr) {
            ListNode* newNode = new ListNode{value, nullptr};
            sentinel->next = newNode;
            size++;
            return;
        }
        ListNode* current = sentinel->next;
        while (current->next != nullptr) {
            current = current->next;
        }
        // Traverse to the last node
        // Create a new node
        ListNode* newNode = new ListNode{value, nullptr};
        current->next = newNode;
        size++;
    }
```

因此，我们实现了链表的插入操作的函数，用于不同的功能：

```C++
//插入函数
    // 在链表头部插入节点
    void insertAtHead(int value) {
        Node* newNode = new Node{value, head}; // 使用初始化列表来初始化新节点
        head = newNode;
    }

    // 在指定节点之后插入新节点
    void insertAfter(Node* current, int value) {
        // 如果当前节点为空，直接返回或抛出异常
        if (current == nullptr) {
            throw std::invalid_argument("Current node is null");
            return;
        }

        // 创建新节点
        Node* newNode = new Node{value, current->next};
        // 将新节点插入到当前节点之后
        current->next = newNode;
    }

    void insertAtPosition(int value, int position) {
        // 如果位置是0或链表为空，则在头部插入
        if (position == 0 || head == nullptr) {
            insertAtHead(value);
            return;
        }

        Node* current = head;
        int currentPosition = 0;
        //设置一个指针，开始执行遍历
    
    
        // 找到插入位置的前一个节点
        while (current->next != nullptr && currentPosition < position - 1) {
            current = current->next;
            currentPosition++;
        }

        // 如果位置超出了链表的末尾，则在末尾插入
        if (currentPosition < position - 1) {
            Node* newNode = new Node{value, nullptr};
            current->next = newNode;
        } else {
            // 插入新节点
            //current代表的是n-1索引上的节点，插入到他的后面就是需要达到的目标索引
            insertAfter(current, value);
        }
    }
    
```

#### 单链表的删除

![erase](erase.png)

原理和单链表的插入大同小异：

- 将`delPtr`前面的一个节点p的引用更改，使其直接指向后面的一个节点即可
- 但是在删除时，要额外关注头和尾的特殊情况
  - 如果是第一个元素，则需要更改头结点的对应的值
  - 如果是尾部元素，直接将上一个元素的应用设置为空指针即可
- **一定要注意delete！！！**

我们先来看一下比较基础的功能实现：删除末尾的元素，思路如下：

- 首先遍历数组走到最后一个元素
  - 由于链表非连续的物理结构的特点，其遍历的过程的复杂度会高于数组，达到了O（n）的时间复杂度。
- 修改倒数第二个元素的引用为空指针
- `delete`掉最后一个元素的内存

```C++
// Delete the last element of the list
    void deletetheend() {
        if (sentinel->next == nullptr) return; // Empty list

        ListNode* current = sentinel->next;
        ListNode* prev = sentinel;

        while (current->next != nullptr) {
            prev = current;
            current = current->next;
        }
        // At this point, current is the last element of the list
        prev->next = nullptr;
        delete current;
        size--;
    }
```

上面的代码已经实现了链表的遍历功能，因此稍微更改一下，便可以实现更多的删除功能。

> 万变不离其宗，修改前一个的引用，删除后一个。要做的只有这两件事！

##### Modify① 根据值删除

```C++
void deleteNode(int key) {
        if (sentinel->next == nullptr) return; // Empty list

        ListNode* current = sentinel->next;
        ListNode* prev = sentinel;

        while (current != nullptr) {
            if (current->val == key) {
                break;
            }
            prev = current;
            current = current->next;
        }

        if (current == nullptr) return; // Node does not exist

        prev->next = current->next;
        delete current;
        size--;
    }
```

##### Modify② 根据索引删除

```C++
void deletetheplace(int index) {
        if (sentinel->next == nullptr) return; // Empty list

        ListNode* current = sentinel->next;
        ListNode* prev = sentinel;
        int count = 0;

        while (current != nullptr && count < index) {
            prev = current;
            current = current->next;
            count++;
        }
        if (count < index) {
            // Index is too large, no corresponding index
            return;
        } else {
            // Then current is the node at the target index
            prev->next = current->next;
            delete current;
            size--;
        }
    }
```

#### 其他操作

**遍历，插入和删除是链表基本操作中最基本的三个操作，现在我们可以轻而易举地实现其他对链表的操作了。**

##### 索引

返回特定索引值的val值

```C++
int at(int index) {
        ListNode* current = sentinel->next;
        // At this point, current points to the address of the first element of the list
        int count = 0;
        if (count >= size) {
            throw out_of_range("Out of range");
        } else {
            while (count < index) {
                current = current->next;
                count++;
            }
            // At this point, current points to the element at the target index
            return current->val;
        }
    }
```

##### 修改

修改特定索引值的val值

```C++
void modify(int index,int replacement){
        ListNode* current = sentinel->next;
        // At this point, current points to the address of the first element of the list
        int count = 0;
        if (count >= size) {
            throw out_of_range("Out of range");
        } else {
            while (count < index) {
                current = current->next;
                count++;
            }
            // At this point, current points to the element at the target index
            current->val=replacement;
        }
    }
```

> 以上两个功能的实现本质上都是先通过链表的遍历找到目标索引所对应的内存（current），然后再在已经找到的内存块上做操作。

##### 按值查找

```C++
int search(int target) {
        int count = 0;
        ListNode* current = sentinel->next;
        // At this point, current points to the address of the first element of the list
        while (current != nullptr) {
            if (current->val == target) {
                return count;
            }
            count++;
            current = current->next;
        }
        return -1;
    }
```

##### 打印列表（可视化）

```C++
void printList() {
        ListNode* current = sentinel->next;
        while (current != nullptr) {
            std::cout << current->val << " -> ";
            current = current->next;
        }
        std::cout << "NULL" << std::endl;
    }
```

##### 置空检查

判断链表是否有元素

```C++
// Check if the list is empty
    bool isempty() {
        if (sentinel->next == nullptr) {
            return true;
        } else {
            return false;
        }
    }
```

### Outlook

Congratulations！现在你已经实现了链表所有的基本操作，包括**初始化，遍历，插入，删除，索引，修改，按值查找，打印等等**。你可以使用自己写的链表去完成一些更加强大的功能！下面是一些展望和未来可以继续改进的方向：

- 优化压缩代码，精简复杂度
- 将代码改写成OOP的形势，写成头文件
- 实现一些更加高级的功能
  - 链表的合并
  - 链表的翻转
  - 链表的计数
  - It's up to you!

## All codes

```C++
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2024-12-08 10:28:07
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2024-12-08 13:48:56
 * @FilePath: \CODE_for_Vscode\C++_project\Linked_list_copy.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2024 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
using namespace std;

struct ListNode {
    int val;
    ListNode *next;
    ListNode(int value = 0, ListNode *next = nullptr) : val(value), next(next) {}
};

struct LinkedList {
    // By default, construct a sentinel node that does not store any value, and its reference points to the first element of the list
    // Initially, it points to nullptr
    ListNode* sentinel;
    int size = 0;

    // Constructor: Initialize the sentinel node, allocate memory on the heap for it
    LinkedList(): sentinel(new ListNode()), size(0) {
        cout << "Created successfully" << endl;
    };

    // Destructor: Iteratively delete all nodes (including the memory pointed to by the sentinel node initially)
    // Once the sentinel points to a null pointer, the destruction process is complete
    ~LinkedList() {
        while (sentinel != nullptr) {
            ListNode* temp = sentinel;
            sentinel = sentinel->next;
            delete temp;
        }
        cout << "Destroyed successfully" << endl;
    }

    // Four insertion functions
    // Insert a node at the head of the list
    void insertAtHead(int value) {
        // Set the reference of the new node to the reference of the sentinel node
        ListNode* newNode = new ListNode{value, sentinel->next};
        // Change the reference of the sentinel node because the first element of the list has changed
        sentinel->next = newNode;
        size++;
    }

    // Insert a new node after the specified node
    // current represents the node after which you want to insert
    void insertAfter(ListNode* current, int value) {
        // If the current node is null, return or throw an exception
        if (current == nullptr) {
            throw std::invalid_argument("Current node is null");
            return;
        }

        // Create a new node
        ListNode* newNode = new ListNode{value, current->next};
        // Insert the new node after the current node
        current->next = newNode;
        size++;
    }

    void insertAtPosition(int value, int position) {
        // If the position is 0 or the list is empty, insert at the head
        if (position == 0 || sentinel->next == nullptr) {
            insertAtHead(value);
            return;
        }

        ListNode* current = sentinel->next;
        int currentPosition = 0;
        // Set a pointer to start the traversal

        // Find the node before the insertion position
        while (current->next != nullptr && currentPosition < position - 1) {
            current = current->next;
            currentPosition++;
        }

        // If the position exceeds the end of the list, insert at the end
        if (currentPosition < position - 1) {
            ListNode* newNode = new ListNode{value, nullptr};
            current->next = newNode;
            size++;
        } else {
            // Insert the new node
            // current represents the node at index n-1, inserting after it achieves the desired index
            insertAfter(current, value);
        }
    }

    // Insert directly at the end
    void insertAtEnd(int value) {
        if (sentinel->next == nullptr) {
            ListNode* newNode = new ListNode{value, nullptr};
            sentinel->next = newNode;
            size++;
            return;
        }
        ListNode* current = sentinel->next;
        while (current->next != nullptr) {
            current = current->next;
        }
        // Traverse to the last node
        // Create a new node
        ListNode* newNode = new ListNode{value, nullptr};
        current->next = newNode;
        size++;
    }

    // Delete operation
    // Delete the node with the specified value
    void deleteNode(int key) {
        if (sentinel->next == nullptr) return; // Empty list

        ListNode* current = sentinel->next;
        ListNode* prev = sentinel;

        while (current != nullptr) {
            if (current->val == key) {
                break;
            }
            prev = current;
            current = current->next;
        }

        if (current == nullptr) return; // Node does not exist

        prev->next = current->next;
        delete current;
        size--;
    }

    // Delete the last element of the list
    void deletetheend() {
        if (sentinel->next == nullptr) return; // Empty list

        ListNode* current = sentinel->next;
        ListNode* prev = sentinel;

        while (current->next != nullptr) {
            prev = current;
            current = current->next;
        }
        // At this point, current is the last element of the list
        prev->next = nullptr;
        delete current;
        size--;
    }

    // Delete the element at a specific index in the list
    void deletetheplace(int index) {
        if (sentinel->next == nullptr) return; // Empty list

        ListNode* current = sentinel->next;
        ListNode* prev = sentinel;
        int count = 0;

        while (current != nullptr && count < index) {
            prev = current;
            current = current->next;
            count++;
        }
        if (count < index) {
            // Index is too large, no corresponding index
            return;
        } else {
            // Then current is the node at the target index
            prev->next = current->next;
            delete current;
            size--;
        }
    }
    void modify(int index,int replacement){
        ListNode* current = sentinel->next;
        // At this point, current points to the address of the first element of the list
        int count = 0;
        if (count >= size) {
            throw out_of_range("Out of range");
        } else {
            while (count < index) {
                current = current->next;
                count++;
            }
            // At this point, current points to the element at the target index
            current->val=replacement;
        }
    }
    // List search
    int at(int index) {
        ListNode* current = sentinel->next;
        // At this point, current points to the address of the first element of the list
        int count = 0;
        if (count >= size) {
            throw out_of_range("Out of range");
        } else {
            while (count < index) {
                current = current->next;
                count++;
            }
            // At this point, current points to the element at the target index
            return current->val;
        }
    }

    int search(int target) {
        int count = 0;
        ListNode* current = sentinel->next;
        // At this point, current points to the address of the first element of the list
        while (current != nullptr) {
            if (current->val == target) {
                return count;
            }
            count++;
            current = current->next;
        }
        return -1;
    }

    // Print list content
    void printList() {
        ListNode* current = sentinel->next;
        while (current != nullptr) {
            std::cout << current->val << " -> ";
            current = current->next;
        }
        std::cout << "NULL" << std::endl;
    }

    // Check if the list is empty
    bool isempty() {
        if (sentinel->next == nullptr) {
            return true;
        } else {
            return false;
        }
    }
};

int main() {
    LinkedList thelklist;
    cout << thelklist.isempty() << endl;
    thelklist.insertAtEnd(6);
    thelklist.insertAtEnd(5);
    thelklist.insertAtEnd(3);
    thelklist.insertAfter(thelklist.sentinel, 2);
    thelklist.insertAtHead(11);
    thelklist.insertAtPosition(7, 4);
    thelklist.deleteNode(2);
    thelklist.deletetheend();
    thelklist.deletetheplace(3);
    cout << thelklist.isempty() << endl;
    cout << thelklist.search(6) << endl;
    cout << thelklist.search(2) << endl;
    thelklist.printList();
    cout << thelklist.size << endl;
    return 0;
}

```

> 改良后的代码将会放在我的github仓库，敬请期待~

## Extensions1 Linked-List-of-both-sides

接下来我们来看双链表。单链表的遍历操作是效率比较低的，达到了O（n）的时间复杂度。同时单链表还有一个致命的缺陷：**单链表只能够实现单向的遍历**，因为节点无法获取其前继节点的地址。

因此，我们需要对链表进行强化，从单链表延伸出**双向链表**：

- 每个节点需要储存**前驱结点**和**后继节点**的地址。
- 增加了**尾节点**（和**头结点**异曲同工）
  - 头结点的前继指针为`nullptr`
  - 尾节点的后继指针为`nullptr`

由于篇幅限制，我们不再详细解释双链表的OOP实现（或者结构体实现），下面笔者简单的介绍一下双链表中的**插入**操作如何实现：

- 动态分配一个新的节点`inserted`
- 遍历到需要插入到的位置，即插入到`current`这个指针指向的节点后面
  - current也可能是头指针
- 更新`inserted`的指针的前驱结点的值（`current`的地址）
- 更新`inserted`的指针的后继结点的值（`current`的后继结点）
- 更新`current`的后继结点的值为当前`inserted`的地址。

{% note warning %}

在自己构造函数实现的时候，一定要注意**每一步的操作顺序！！！**例如在这里2,3两步的顺序就不可以调换，否则`current`后继结点的值会被抹去，导致**链表中断**。

{% endnote %}

优化后，双链表便有了双向遍历的能力。同时，双链表在**插入和删除**等操作上也具有更高的便利性。例如，在访问某个节点的时候需要删除该节点，则对应单链表而言，需要**从头开始遍历到这个节点的前驱结点**，然后将后继节点和前驱结点直接建立联系。（**本质原因：单链表的设计只允许单向的遍历操作，不可以走回头路**）

但是，在双链表中，我可以**直接获取特定节点的前驱结点和后继结点的地址**，可以直接进行删除操作。

## Extensions2 Bidirectional circular linked lists

**循环链表**很大的不同之处在于**不再需要设置头尾节点**。但是，为了保证与双向链表的兼容性，可以保留头尾节点，而是**将头结点的前驱结点的地址设置为尾节点的地址，将尾节点的后继结点的地址设置为头结点的地址**。（这样就进需要修改两个值便可以将双向链表升级为**双向循环链表**）。

> 但是这种方式有一种弊端就是链表成环后，**头结点和尾节点并不储存有效的信息，比较的冗余**。

因此，一般更通用的做法是不设置头结点和尾节点，这样只要讨论**空链表的特殊性**，其他操作也能够正常的完成。

[推荐这一篇博客，不过是C语言的]([数据结构_链表_单向循环链表 & 双向链表的初始化、插入、删除、修改、查询打印（基于C语言实现） - 逸風明 - 博客园](https://www.cnblogs.com/wyfm/p/18533925))

## References

《C++程序设计——思想与方法》

《Hello 算法》

《数据结构：思想和实现》

[Hello 算法](https://www.hello-algo.com/)

> THE END

