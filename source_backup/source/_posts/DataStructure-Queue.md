---
title: DataStructure-Queue
date: 2025-01-22 14:41:08
index_img: /img/cover/queue.jpg
excerpt: This Blog focuses on the use of Queue.
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Queue
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Data_Structure Queue

## Introduction

在之前的学习过程中，我们主要介绍了**线性数据结构**——线性表，同时介绍了一种非常特殊的线性表：**栈**。栈的关键在于**约束了数据结构输入数据和输出数据的顺序**：即**LIFO**的顺序。（不过有个前提，栈是线性的数据结构，因此数据结构的排列一定是线性的）

**LIFO**的顺序可以帮助我们完成很多事，例如在上一讲的末尾：**括号匹配器和简单的计算器（中缀转后缀）**，尤其在**输入和输出顺序不一致的情况下**，栈可以发挥很大的作用。

> 这再次印证了那句话：数据结构的本质是对实际情形的抽象数据建模。

我们可以沿着这条线继续思考，我们是否可以人为更改**输入数据顺序和输出数据顺序的约束**，而创建新的数据类型？答案当然是肯定的，今天，我们来介绍一种新的数据结构——队列（Queue）

## Definition

**栈**：元素的插入和删除操作只能在**栈的一端**进行，这一端称为 **栈顶（Top）**，另一端称为 **栈底（Bottom）**。

**队列**：元素的插入操作在队列的一端进行，称为 **队尾（Rear）**，删除操作在另一端进行，称为 **队头（Front）**。

> 双向队列是一种允许在 **两端** 进行插入和删除操作的线性数据结构。它结合了栈和队列的特性，既可以从队头操作，也可以从队尾操作。
>
> 双向队列即有栈的性质，也有队列的性质，后续会专门花一讲的时间辨析这三者使用上的区别。

![队列的先入先出规则 Hello算法](https://www.hello-algo.com/chapter_stack_and_queue/queue.assets/queue_operations.png)

形象化理解非常的容易，就像元素在**排队一样**，满足先来后到的顺序，这也是队列区别于栈的最显著的差别，也是队列最核心的性质：**FIFO (First in First out)**，也就是说，**队列不会导致输入顺序和输出顺序的差异**。

## Basic Usage

和栈类似，队列的基本操作如下：

- 创建一个队列`create()`
- 入队`push(const T& value)`：将`value`插入成为**队尾元素**
- 出队`pop()`：使队头元素出队并且返回这个元素的值。
- 读队头元素`getHead()`
- 判断队空`isEmpty()`

下文代码是实现该定义的抽象基类：

```cpp
//Abstract class
template <class T>
class queue{
    public:
        virtual ~queue(){}
        virtual bool isEmpty() const =0;
        virtual T getHead() const =0;
        virtual T pop()=0;
        virtual void push(const T& value)=0;
};
//抽象基类仅仅定义接口，没有具体的数据成员
```

## Implementation

### Sequential Implementation

> 下面的思考和优化过程非常的精彩，请反复回味。

下文实现队列的顺序实现，即使用**一块动态内存来存储线性的所有数据**。在栈中，由于所有的操作都是在栈顶完成的，因此，我们只需要**对栈顶元素进行操作**，时间复杂度为O(1)。但是，在队列中**涉及双端操作：即输入端和输出端不在一起**。这就会带来时间复杂度上的提升：例如固定队首元素，并且将此作为**出队口**，（就像超市结账人们在收银台处排队一样），入队的操作复杂度为O(1)，但是出队的时候所有元素都要前移一格，时间复杂度为O(n)。

这并不符合我们的预期——**我们希望出队和入队都达到最简单的O（1）时间复杂度**，如何优化？

这就是第一个优化策略：**维护一个类似于头指针的东西**，在顺序实现中，就是一个`int`值`front`来**维护队头元素的索引**。同时我们也会维护一个`int`值`rear`来锚定队尾元素。在队列为空的时候，设置`front`和`rear`均为-1。

![基于数组实现队列的入队出队操作](https://www.hello-algo.com/chapter_stack_and_queue/queue.assets/array_queue_step1.png)

这样很好的解决了我们的问题，实现了入队和出队的时间复杂度均为常数时间复杂度。但是这有一个很隐蔽的问题：空间利用率问题。**出队是数组元素没有移动而是索引`front`向后移动**，这样会造成空间上的浪费，`front`索引之前的部分无法再存储新的数据元素，这样的情况会随着出队元素的不断增多而更加凸显出来。

于是，我们需要第二个优化策略：**循环队列**。那这样两个索引在分配的内存上无论怎么移动都不会造成空间使用上的浪费了，具体实现也很简单：**使用取余操作**。

```cpp
//非循环队列的入队操作：
rear++;
//循环队列的入队操作：
rear=(rear+1)%Maxsize;

//非循环队列的出队操作:
front++;
//循环队列的出队操作：
front=(front+1)%MAxsize;
```

**使用取余操作可以保证`rear`和`front`这两个索引值始终都在`[0,Maxsize-1]`这个区间范围内**。

这样看似就没有问题了！真的如此吗？

我们重新回看`rear`和`front`这两个索引值，这两个索引值表示的是什么？`front`指向的是首元素，而`rear`指向的是尾元素+1。当初始建队时，这两个值都设置为0，如果不断入队，rear不断递增直到走完了一个周期，取余后**和front重合**，此时队列的元素达到了**100%**的空间利用率，但是无法判断**队满还是队空**。

因此，一定的牺牲肯定是必要的，就像链表中的头结点不储存值一样，**循环队列需要保证rear索引所在的地方不存储值**，即理论上队列的最大存储值的个数是`Maxsize-1`个。

![循环队列](https://docs.oldtimes.me/c.biancheng.net/cpp/uploads/allimg/140713/1-140G32234251B.jpg)

> 教材上设置的是**front**不存储值，没有太大的影响。

做了这样的规定之后，我们就能确定队满和队空了。

> 实际上还有一种操作，维护一个int值表示队列的长度，此时就可以达到100%的空间利用率

```cpp
//队满的判定条件：
(rear+1) % Maxsize == front;
    //注意这里是对rear做操作而不是front，因为对rear做操作代表着入队操作。
//队空的判定条件：
front == rear;
```

理解了上面精彩的演绎推理过程，我们轻而易举的可以实现队列的顺序实现：

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-01-22 15:06:31
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-01-22 17:23:51
 * @FilePath: \CODE_for_Vscode\Data_structure\Class_implementation\Stack.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
/*
This is the implementation of the stack
*/

#pragma once
#include <stdexcept>
#include <iostream>
using namespace std;


//Abstract class
template <class T>
class queue{
    public:
        virtual ~queue(){}
        virtual bool isEmpty() const =0;
        virtual T getHead() const =0;
        virtual T pop()=0;
        virtual void push(const T& value)=0;
};
//抽象基类仅仅定义接口，没有具体的数据成员

//Sequential Implementation of stack
template <class T>
class seqQueue:public queue<T>{
    private:
        T* elem;//the elements
        int maxsize;
        int front,rear;
        void DoubleSpace();//Tool functions for space extensions
    public:
        seqQueue(int size=10);
        ~seqQueue();
        bool isEmpty() const;
        void push(const T& value);
        T getHead() const;
        T pop();

        //Only for debugging
        void print() const;
        int length() const;
};

template <class T>
seqQueue<T>::seqQueue(int size):maxsize(size){
    elem = new T[maxsize];
    //but the maximum capacity is maxsize-1
    front = rear =0;
}

template <class T>
seqQueue<T>::~seqQueue(){
    delete[] elem;
}

template <class T>
T seqQueue<T>::getHead() const{
    if (isEmpty()) {
        throw std::runtime_error("Queue is empty");
    }
    return elem[front];
}

template <class T>
bool seqQueue<T>::isEmpty() const{
    return front==rear;
}

template <class T>
void seqQueue<T>::push(const T& value){
    //入队操作，需要对rear操作
    if((rear+1) % maxsize == front){
        //judge if the queue is full
        DoubleSpace();
    }else{
        elem[rear]=value;
        rear=(rear+1)%maxsize;
    }
}

template <class T>
T seqQueue<T>::pop(){
    if (isEmpty()) {
        throw std::runtime_error("Queue is empty");
    }
    //出队操作，对front操作
    T temp = elem[front];//the top element of the queue
    front = (front+1)%maxsize;
    return temp;
}

template <class T>
void seqQueue<T>::DoubleSpace(){
    //double the space of the queue
    T* tmp=elem;
    elem = new T[2*maxsize];
    for(int i=0;i<maxsize-1;i++){
        elem[i] = tmp[(front+i)%maxsize];
        //you only need to copy maxsize-1 elements!
    }
    front = 0;
    rear = maxsize-1;
    maxsize*=2;
    delete[] tmp;
}

template <class T>
void seqQueue<T>::print()const {
    //print the whole queue
    if(isEmpty()){
        cout<<"The queue is empty!"<<endl;
        return;
    }else{
        cout<<"The queue has "<<length()<<" members."<<endl;
        int index = front;
        while(index != rear){
            cout<<elem[index]<<" ";
            index = (index+1)%maxsize;
        }
        cout<<endl;
    }
}

template <class T>
int seqQueue<T>::length() const{
    if(rear >= front) return rear-front;
    else return rear+maxsize-front;
}
//The following function implements some small debugging features.
int main(){
    seqQueue<int> list;
    list.print();
    list.push(2);
    list.push(5);
    cout<<list.isEmpty()<<endl;
    list.print();
    cout<<list.pop();
    cout<<list.pop();
    cout<<list.isEmpty()<<endl;
    return 0;
}
```

### Linked Implementation

队列的链接实现被称为**链接队列**。由于队列不会涉及**遍历**（而这是链接实现所不擅长的），只会设计单个元素的**出队和入队**等操作，因此队列的链接实现使用最简单的**单链表**即可。

同时，出队就将队首元素释放，入队就在队尾新增一个元素，因此也不涉及**循环队列**的使用。

唯一不同的是，为了快速的定位到队尾元素，链接队列需要额外储存**队尾元素的地址**。

```cpp
template <class T>
class linkQueue : public queue<T>{
    private:
        struct Node
        {
            T data;
            Node* next;
            //parameterized constructor(default)
            Node(const T& value = 0, Node *p = nullptr){
                data = value;
                next = p;
            }
            //destructor
            ~Node(){}
        };
        Node *front, *rear;
        //two pointers point at the front or the end of the queue.
    public:
        linkQueue();
        //This constructor is used to create a new linked queue.
        //Todo: implement copy constructor and the overload of = (assignment)
        ~linkQueue();
        bool isEmpty() const;
        T pop();
        T getHead() const;
        void push(const T& value);

        //for debugging
        void print() const;
        int length() const;
        
};


//Implementation of linked queue
template <class T>
linkQueue<T>::linkQueue(){
    //set nullptr for both pointers
    front = rear = nullptr;
    //it is also the judgement of an empty queue
}

template <class T>
linkQueue<T>::~linkQueue(){
    Node * temp;
    while(front != nullptr){
        //simulate the process of leaving the queue until the queue is empty.
        temp = front;
        front = front -> next;
        delete temp;
    }
}

template <class T>
bool linkQueue<T>::isEmpty() const {
    return front == nullptr;
}

template <class T>
T linkQueue<T>::getHead() const{
    if (isEmpty()) {
        throw std::runtime_error("Queue is empty");
    }
    return front -> data;
}

template <class T>
void linkQueue<T>::push(const T& value){
    if(isEmpty()){
        front = rear = new Node(value);
    }else{
        Node *newnode = new Node(value);
        rear -> next = newnode;
        rear = rear -> next;
        //! ensure the rear -> next often points to the nullptr
    }
}

template <class T>
T linkQueue<T>::pop(){
    if (isEmpty()) {
        throw std::runtime_error("Queue is empty");
    }else{
        T ans = front -> data;
        Node* tmp = front;
        front = front -> next;
        if(front == nullptr){
            rear = nullptr;
            //now the queue is empty!
            //This is necessary, because before the pop process, the front and the rear pointer points at the same space
            //now front moves to nullptr, rear pointer becomes INVALID! 
        }
        delete tmp;
        return ans;
    }
}

template <class T>
int linkQueue<T>::length() const {
    if(isEmpty()){
        return 0;
    }
    int length=0;
    Node* traverseptr = front;
    while(traverseptr != nullptr){
        traverseptr = traverseptr -> next;
        length++; 
    }
    return length;
}

template <class T>
void linkQueue<T>::print()const {
    if(isEmpty()){
        std::cout << "The queue is empty!" << std::endl;
        return;
    }else{
        int length=0;
        Node* traverseptr = front;
        while(traverseptr != nullptr){
            std::cout << traverseptr -> data << " ";
            traverseptr = traverseptr -> next;
            length++; 
        }
        std::cout << std::endl;
        std::cout << "The queue has " << length << " elements" << std::endl;
    }
}

//The main function is used for Debugging only.
int main(){
    seqQueue<int> list;
    list.print();
    list.push(2);
    list.push(5);
    std::cout << list.isEmpty() << std::endl;
    list.print();
    std::cout << list.pop();
    std::cout << list.pop();
    std::cout << list.isEmpty() << std::endl;
    //The debug of link queue
    std::cout << std::endl;
    linkQueue<int> list2;
    list2.print();
    for(int i = 0; i < 5; i++){
        list2.push(i * i);
        list2.print();
    }

    std::cout << list2.isEmpty() << std::endl;
    for (int i = 0; i < 5; i++)
    {
        std::cout << list2.pop() <<std::endl;
        list2.print();
    }
    
    std::cout << list2.isEmpty() << std::endl;
    return 0;
}
```



## Queue in STL

在C++标准模板库（STL）中，`queue` 是一种容器适配器，用于实现先进先出（FIFO）的数据结构。它基于其他容器（如 `deque` 或 `list`）实现，默认使用 `deque` 作为底层容器。

1. **先进先出（FIFO）**：元素从队尾插入，从队头移除。
2. **操作受限**：只允许在队尾插入元素，在队头移除元素，不支持随机访问。

### 常用操作
- **push()**：在队尾插入元素。
- **pop()**：移除队头元素。
- **front()**：访问队头元素。
- **back()**：访问队尾元素。
- **empty()**：检查队列是否为空。
- **size()**：返回队列中元素的数量。

```cpp
#include <iostream>
#include <queue>

int main() {
    std::queue<int> q;

    q.push(10); // 队尾插入10
    q.push(20); // 队尾插入20
    q.push(30); // 队尾插入30

    std::cout << "队头元素: " << q.front() << std::endl; // 输出10
    std::cout << "队尾元素: " << q.back() << std::endl;  // 输出30

    q.pop(); // 移除队头元素10

    std::cout << "队头元素: " << q.front() << std::endl; // 输出20

    return 0;
}
```

### 底层容器

`queue` 默认使用 `deque`，但可以通过模板参数指定其他容器，如 `list`：
```cpp
std::queue<int, std::list<int>> q;
```

{% note primary %}

## `deque` in STL

`deque`（**双端队列，全称 double-ended queue**）是 C++ 标准模板库（STL）中的一种序列式容器，支持在两端高效地插入和删除元素。与 `vector` 类似，`deque` 也支持随机访问，但在某些操作上比 `vector` 更加灵活。

### **主要特点**
1. **双端操作**：
   - 可以在队列的头部和尾部高效地插入和删除元素。
   - 支持 `push_front()`、`push_back()`、`pop_front()` 和 `pop_back()` 操作。

2. **动态扩容**：
   - `deque` 的内部实现通常由多个固定大小的块（buffer）组成，因此它可以在两端动态扩展，而不需要像 `vector` 那样在扩容时复制所有元素。

3. **随机访问**：
   - 支持通过下标（`[]`）或 `at()` 方法随机访问元素，时间复杂度为 \(O(1)\)。

4. **内存非连续**：
   - `deque` 的元素并不存储在连续的内存空间中，而是分布在多个块中。这与 `vector` 不同，`vector` 的元素是连续存储的。

### **常用操作**
以下是 `deque` 的常用成员函数：

- **插入和删除**：
  - `push_front(x)`：在头部插入元素 `x`。
  - `push_back(x)`：在尾部插入元素 `x`。
  - `pop_front()`：删除头部元素。
  - `pop_back()`：删除尾部元素。
  - `insert(pos, x)`：在指定位置插入元素 `x`。
  - `erase(pos)`：删除指定位置的元素。

- **访问元素**：
  - `front()`：返回头部元素的引用。
  - `back()`：返回尾部元素的引用。
  - `operator[]` 或 `at(index)`：通过索引访问元素。

- **容量相关**：
  - `size()`：返回当前元素的数量。
  - `empty()`：检查容器是否为空。
  - `resize(n)`：调整容器的大小为 `n`。

- **其他操作**：
  - `clear()`：清空容器中的所有元素。
  - `swap(deque)`：交换两个 `deque` 的内容。

```cpp
#include <iostream>
#include <deque>

int main() {
    std::deque<int> dq;

    // 在尾部插入元素
    dq.push_back(10);
    dq.push_back(20);

    // 在头部插入元素
    dq.push_front(5);
    dq.push_front(1);

    // 访问元素
    std::cout << "头部元素: " << dq.front() << std::endl; // 输出 1
    std::cout << "尾部元素: " << dq.back() << std::endl;  // 输出 20

    // 随机访问
    std::cout << "第二个元素: " << dq[1] << std::endl;    // 输出 5

    // 删除头部元素
    dq.pop_front();
    std::cout << "删除后头部元素: " << dq.front() << std::endl; // 输出 5

    // 遍历 deque
    for (int i : dq) {
        std::cout << i << " "; // 输出 5 10 20
    }
    std::cout << std::endl;

    return 0;
}
```

### **`deque` 的内部实现**
`deque` 通常由多个固定大小的块（buffer）组成，这些块通过一个中央控制器（通常是数组或指针数组）管理。这种设计使得 `deque` 在两端插入和删除元素时非常高效，同时支持随机访问。

- **优点**：
  - 两端插入和删除的时间复杂度为 *O(1)*。
  - 不需要像 `vector` 那样在扩容时复制所有元素。

- **缺点**：
  - 内存分布不连续，可能导致缓存不友好。
  - 随机访问的性能略低于 `vector`。

### **适用场景**
1. 需要在两端频繁插入和删除元素的场景。
2. 需要随机访问，但不需要内存连续性的场景。
3. 作为 `queue` 或 `stack` 的底层容器。

### **`deque` 与 `vector` 的对比**
| 特性              | `deque`                        | `vector`                                   |
| ----------------- | ------------------------------ | ------------------------------------------ |
| **内存布局**      | 非连续                         | 连续                                       |
| **随机访问性能**  | 略低                           | 高                                         |
| **两端插入/删除** | 高效（\(O(1)\)）               | 尾部高效（\(O(1)\)），头部低效（\(O(n)\)） |
| **扩容机制**      | 按需分配新块，无需复制所有元素 | 需要复制所有元素                           |
| **缓存友好性**    | 较差                           | 较好                                       |

> The above content is generated by `DeepSeek V3`.
>
> 后文会专门花一讲的时间梳理各种线性数据结构之间的异同~

{% endnote %}

## Applications of Queue

### 单服务器模拟

### 多服务器模拟（优先级队列）

在多服务器模拟中，**先处理事件的离开时间**可能晚于**后处理事件对应的离开事件**，如果使用一个单纯的简单队列无法实现，因此在此处需要使用**优先级队列**，时间戳更靠前的（绝对时间）优先级更高。

- 初始化：产生所有到达事件，存入**事件队列**。

- 模拟器：
	- **取出一个到达事件**：
	  - 如果有空
	  	- 如果存在空闲柜台，加上服务时间生成离开时间
	  	- 将离开时间插入队列
	  - 如果没空
	  	- 加入等待队列
	- **取出一个离开事件**：
	  - 如果等待队列非空：
	    - 服务（类似于**取出到达事件——有空**）
	  - 空闲柜台 + 1
	
	
