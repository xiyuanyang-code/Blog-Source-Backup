---
title: DataStructure-String
date: 2025-02-02 17:02:04
index_img: /img/cover/string.jpg
excerpt:  This article introduces sequential and linked implementation of string and some basic string-matching algorithms including KMP algorithms.
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - string
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Data Structure: String

## Abstract

This article reconstructs the underlying code of the **string** class from the perspective of data structures, including **array-based sequential implementation** and **block list-based linked implementation**. It also provides the usage of related functions in the string library. Subsequently, the article explains the classic string matching algorithm: the **KMP** algorithm and its applications, and finally introduces the concept of regular expressions.

## Introduction

字符串在编程问题中很常见，在初学C++时，我们可以把字符串看成**char类型的数组**（C风格的字符串），同时C++引入了新的字符串类型：**string**。`string`类相比于传统的`char*`有着很多优势：

- 根据`size`动态分配内存

- 不同手动回收堆上的内存

- 支持许多字符串操作

在笔者的[这篇博客](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)中，为了详细解释OOP中动态内存分配的使用，从一个`stringbad`类出发不断修改最终形成了一个还算健全的`string`类。本章作为**线性数据结构**的尾声，我们从**数据结构**的视角从底层重新审视`string`类并重写轮子。

## Definition

显而易见地，`string`类是一种线性数据结构，是**线性表的一种特殊情况**：

- 线性表的每一个元素都是**ASCII字符**。

- 线性表有值等现实意义：例如"IloveSJTU"是一个字符串，是一个线性表，也可以说**用双引号括起来的字符序列是字符串的值**。

> 第二点有点钻牛角尖，读者只需了解：**字符串是具有特殊的现实意义的**，因此可以实现很多新的操作（例如运算符重载等等），而这些操作对于一般抽象的线性表而言很多是没有意义的。

## Implementation

接下来笔者给出线性表的常用操作：

- 求字符个数 `length()`

- 输出函数 `disp()`

- **经典增删查改四件套**
  
  - 插入函数 `insert(s1, start, s2)` ，在start处将s2插入进s1中
  
  - 删除子串 `remove(s, start, len)`
  
  - 查找子串（**这个问题很经典，我们会在KMP算法中实现**）
  
  - 修改：**在这里我们实现字符串的赋值函数** `copy(s1, s2)`

- 取子串 `substr(s, start, len)`

- 字符串连接 `cat(s1, s2)`

- 比较函数
  
  - `equal(s1, s2)`，`greater(s1, s2)`，`greaterEqual(s1, s2)`，`less(s1, s2)`，`lessEqual(s1, s2)`

### Sequential Implementation

下面我们来构思类的声明：

数据成员：一个char类型的指针（顺序存储字符），一个int值储存字符的大小。

> 也可以将int值改写成一个**返回int值的函数**，但是这样做每一次求length都需要遍历字符串时间复杂度为O(n)，这里采用以空间换时间的做法。

```cpp
/*
This is the implementation of hand-made string
*/
#include <stdexcept>
#include <iostream>
#include <algorithm> //just for the min function
#include <cstring>


//Sequential implementation of string
class seqString{
    friend seqString operator+(const seqString& s1, const seqString& s2);
    friend bool operator==(const seqString& s1, const seqString& s2);
    friend bool operator!=(const seqString& s1, const seqString& s2);
    friend bool operator>(const seqString& s1, const seqString& s2); 
    friend bool operator>=(const seqString& s1, const seqString& s2);
    friend bool operator<(const seqString& s1, const seqString& s2);
    friend bool operator<=(const seqString& s1, const seqString& s2);
    friend std::istream& operator>>(std::istream& is,  seqString& s);
    friend std::ostream& operator<<(std::ostream& os, const seqString& s);

    private:
        char *data;
        int len = 0;

    public:
        seqString(const char* s="");     
        seqString(const seqString& other);   
        ~seqString();
        seqString& operator=(const seqString& other);
        seqString& operator+=(const seqString& other);
        seqString& operator-=(const seqString& other);
        char operator[](int index) const;
        void insert(int start, const seqString& other, int insertsize = -1);
        void remove(int deletelength, int start =-1);
        int length() const;
        bool isEmpty() const;
        seqString substr(int start, int leng) const;
};

//Defintion of Sequenial String

//default constructer
seqString::seqString(const char*s){
    //first get the value of length;
    while(s[len] != '\0'){
        len++;
    }
    data = new char [len+1];
    for(int i = 0; s[i] != '\0'; i++){
        data[i] = s[i];
    }
    data[len] = '\0';
}

//copy constructor
seqString::seqString(const seqString& other){
    len = other.len;
    data = new char [len+1];
    for(int i = 0; i <= len; i++){
        data[i] = other.data[i];
    }
}

//destructor
seqString::~seqString(){
    delete[] data;
}

//overload of assignment operator
seqString& seqString::operator=(const seqString& other){
    if (this == &other) return *this;
    delete[] data;
    len = other.len;
    data = new char [len+1];
    for(int i = 0; i <= len; i++){
        data[i] = other.data[i];
    }
    return *this;
}

//overload of "+=" operator
seqString& seqString::operator+=(const seqString& other){
    this -> insert(len, other);
    return *this;
}

//overload of "-=" operator
seqString& seqString::operator-=(const seqString& other){
    seqString tmp = substr(len - other.length(), other.length());
    if (tmp == other){
        this -> remove(len - other.length(), other.length());
        return *this;
    }else{
        throw std::runtime_error("Unmatched string");
    }
}

//overload of index operator
char seqString::operator[](int index) const{
    if(index < 0 || index >= len){
        throw std::runtime_error("Invalid index!");
    }else{
        return data[index];
    }
}

//insert a string in the certain position
void seqString::insert(int start, const seqString& other, int insertsize){
    if(insertsize == -1){
        //This means insert the whole size of other
        insertsize = other.length();
    }

    if( insertsize < 0 || insertsize > other.length()){
        throw std::runtime_error("Invalid insert size!");
    }
    else if( start < 0 || start > len){
        throw std::runtime_error("Invalid starting position!");
    }
    else{
        char *tmp = data;
        len += insertsize;
        data = new char [len+1];
        for(int i = 0; i < start; i++){
            data[i] = tmp[i];
        }
        for(int i = 0; i< insertsize; i++){
            data[start + i] = other.data[i];
        }
        for(int i = start; tmp[i] !='\0'; i++){
            data[i + insertsize] = tmp[i];
        }
        data[len] = '\0';
        delete[] tmp;
    }
}

//remove a substring from a certain position
void seqString::remove(int deletelength,int start ){
    if(start == -1){
        //then delete the last leng characters from the back
        start = len - deletelength;
        //len represents the length of the whole string;
    }

    if(start < 0 || start >= len){
        throw std::runtime_error("Invalid starting position!");
    }

    if(start + deletelength >= len){
        //delete all the characters after the start index
        data [start] = '\0';
        len = start;
        char * tmp = data;
        //recycle the memory
        data = new char [len+1];
        for(int i = 0; i<= len; i++){
            data[i] = tmp[i];
        }
        delete[] tmp;
    }else{
        len -= deletelength;
        char *tmp = data;
        data = new char [len+1];
        for(int i = 0; i < start; i++){
            data[i] = tmp[i];
        }
        for(int i = start + deletelength; tmp[i] != '\0'; i++){
            data[i - deletelength] = tmp[i];
        }
        data[len] = '\0';
        delete[] tmp;
    }
}

//get the length of the string
int seqString::length() const{
    return len;
}

//judge whether the string is empty
bool seqString::isEmpty() const{
    return len == 0;
}

//get a substring from a certain position and a certain length
seqString seqString::substr(int start, int sublength) const{
    if(start < 0 || start >= len){
        throw std::runtime_error("Invalid starting position!");
    }else{
        if(sublength + start >= len){
            sublength = len - start;
        }

        seqString tmp; //the target substring
        tmp.len = sublength;
        delete tmp.data;
        tmp.data = new char[tmp.len + 1];
        for(int i = 0; i < sublength; i++){
            tmp.data[i] = data[start + i];
        }
        tmp.data[tmp.len] = '\0';
        return tmp;
    }
}

//overload of plus operation
seqString operator+(const seqString& s1, const seqString& s2){
    seqString tmp;
    tmp.len = s1.len + s2.len;
    delete[] tmp.data;
    tmp.data = new char[tmp.len + 1];
    for(int i = 0; i < s1.len; i++){
        tmp.data[i] = s1.data[i];
    }
    for(int i = 0; i < s2.len; i++){
        tmp.data[i + s1.len] = s2.data[i];
    }
    tmp.data[tmp.len] = '\0';
    return tmp;
}

//several judgements
bool operator==(const seqString& s1, const seqString& s2){
    if(s1.len != s2.len) return false;
    for(int i = 0; i< s1.len; i++){
        if(s1.data[i] != s2.data[i]) return false;
    }
    return true;
}

bool operator!=(const seqString& s1, const seqString& s2){
    return !(s1==s2);
}

bool operator>(const seqString& s1, const seqString& s2){
    int minlength = std::min(s1.len, s2.len);
    for(int i = 0; i < minlength; i++){
        if(s1[i] != s2[i]) return s1[i] > s2[i];
    }
    return s1.len > s2.len;
}

bool operator>=(const seqString& s1, const seqString& s2){
    return s1 > s2 || s1 == s2;
}

bool operator<(const seqString& s1, const seqString& s2){
    return !(s1 >= s2);
}

bool operator<=(const seqString& s1, const seqString& s2){
    return s1 < s2 || s1 ==s2;
}

//the overload function of << and >> operator
std::istream& operator>>(std::istream& is, seqString& s) {
    const int bufferSize = 1024;
    char buffer[bufferSize];
    is >> buffer;

    int inputLen = std::strlen(buffer);

    if (s.data) {
        delete[] s.data;
    }

    s.data = new char[inputLen + 1];
    std::strcpy(s.data, buffer);
    s.len = inputLen;
    return is;
}

std::ostream& operator<<(std::ostream& os, const seqString& s){
    os << s.data;
    return os;
}


//main function is used for debugging only
// Main function for debugging
int main() {
    seqString s1("Hello");
    seqString s2("World");

    // Test operator+
    seqString s3 = s1 + s2;
    std::cout << "s1 + s2: " << s3 << std::endl;

    // Test operator+=
    s1 += s2;
    std::cout << "s1 += s2: " << s1 << std::endl;

    // Test operator-
    s1 -= s2;
    std::cout << "s1 -= s2: " << s1 << std::endl;

    // Test operator[]
    std::cout << "s1[1]: " << s1[1] << std::endl;

    // Test insert
    s1.insert(1, s2);
    std::cout << "s1 after insert: " << s1 << std::endl;

    // Test remove
    s1.remove(5, 1);
    std::cout << "s1 after remove: " << s1 << std::endl;

    // Test substr
    seqString s4 = s1.substr(1, 3);
    std::cout << "s1.substr(1, 3): " << s4 << std::endl;

    // Test comparison operators
    std::cout << "s1 == s2: " << (s1 == s2) << std::endl;
    std::cout << "s1 != s2: " << (s1 != s2) << std::endl;
    std::cout << "s1 > s2: " << (s1 > s2) << std::endl;
    std::cout << "s1 < s2: " << (s1 < s2) << std::endl;

    return 0;
}
```

### Linked Implementation

接下来我们进行字符串的链接实现。最直接的方法就是使用单向链表，如下图所示（图选自Hello 算法）

![Linked List](https://www.hello-algo.com/chapter_array_and_linkedlist/linked_list.assets/linkedlist_definition.png)

但是，这种方法 **对空间的利用率太低**，一个char字符是1字节，而存储一个指针变量 **需要8字节（对于64位系统而言）**。因此，我们可以使用 **块状链表** 来提高链表的空间利用率。

![linked list from OI wiki](https://oi-wiki.org/ds/images/kuaizhuanglianbiao.png)

可以把块状链表当做 **链表和连续数组** 的一种tradeoff，链表有利于使用分裂的离散内存，而数组弥补了链表遍历复杂度高和空间利用率低等问题！

不过块状链表有个比较复杂的问题：**元素的插入和删除**依旧会带来较大的时间复杂度。如果每一个子数组都占满的话，那插入和删除的时间复杂度会是O(n)级别的，因此，**空间换时间**在此处仍然必要，**我们需要再每个子数组中空余一部分空间方便后续的插入删除操作。**同时，我们可能需要使用 **分裂节点** 或者 **合并节点** 的方法，让每一个数组中的空间利用率保持稳定。

例如下面的例子：

![Block List](https://ooo.0x0.ooo/2025/02/04/OGHQYx.png)

如果该链表需要完成如下操作：**在F后面插入字符串UVXYZ**，那可以细分为这几步：

- 分裂结点，让 EF 和 G 分离。
- 将 **UVXYZ** 打包成一个节点，这里因为超出限制，因此打包成了两个节点。
- 将打包好的节点（或者可以被称为一个**子串**） 插入到对应的位置。
- **归并**利用率比较低的节点。

![Node splitting and merging](https://ooo.0x0.ooo/2025/02/04/OGH0qj.png)

有了这些准备知识，我们可以给出字符串链接实现的声明：

```cpp
class linkString{
    friend linkString operator+(const linkString& s1, const linkString& s2);
    friend bool operator==(const linkString& s1, const linkString& s2);
    friend bool operator!=(const linkString& s1, const linkString& s2);
    friend bool operator>(const linkString& s1, const linkString& s2); 
    friend bool operator>=(const linkString& s1, const linkString& s2);
    friend bool operator<(const linkString& s1, const linkString& s2);
    friend bool operator<=(const linkString& s1, const linkString& s2);
    friend std::istream& operator>>(std::istream& is,  linkString& s);
    friend std::ostream& operator<<(std::ostream& os, const linkString& s);

    struct node
    {
        int size;    //valuable numbers of chars in a block node
        char *data;
        node *next;

        //constructor
        node(int maxsize_ = 1, node *n_ = nullptr){
            data = new char [maxsize_];
            size = 0;
            next = n_;
        }
    };

    private:
        node *head; //head pointer
        int length;  //length of the string
        int nodesize; //capacity for every block node

        //several private function tools
        void clear();  //release all the memories
        void findPos(int start, int& pos, node *&p) const; // find the position of the certain node
        void split(node *p, int pos);  //split nodes
        void merge(node *p);  //merge nodes

    public:
        linkString(const char* s = "");          // Constructor with default parameter
        linkString(const linkString& other);     // Copy constructor
        ~linkString();                           // Destructor
        linkString& operator=(const linkString& other);  // Assignment operator overload
        linkString& operator+=(const linkString& other); // Compound assignment operator overload (concatenation)
        linkString& operator-=(const linkString& other); // Compound assignment operator overload (removal)
        char operator[](int index) const;                // Subscript operator overload
        void insert(int start, const linkString& other, int insertsize = -1); // Insert operation
        void remove(int deletelength, int start = -1);   // Remove operation
        int length() const;                              // Get the length of the string
        bool isEmpty() const;                            // Check if the string is empty
        linkString substr(int start, int leng) const;    // Get a substring
};
```

- 研究表明，**块状链表的节点容量和节点个数相同的时候**，算法效率最高。
  - 因此，节点容量一个设置为：`sqrt(length)` ,来尽可能提高算法的效率。

{% note primary %}

**附上数学推导**：

- 块状链表的查找操作需要遍历块（链表部分）和在块内查找（数组部分）。
- 如果节点容量过大，块内查找的时间会增加（因为数组部分变长）。
- 如果节点容量过小，块的数量会增加，导致链表部分变长，遍历块的代价增加。
- 当节点容量和节点个数相同时，查找和修改的代价达到平衡，整体效率最高。

假设块状链表有 \( n \) 个元素，块的大小为 \( B \)，块的数量为 \( M \)，则 `n = BM`。

 查找、插入、删除操作的时间复杂度为 `O(M + B)`（考虑最坏的时间复杂度）。

做一点小小的数学变形：`O(M+B)` = `O(n/B +B)`， 是一个对勾函数！因此在`sqrt(n)` 的时候取到最小值。

{% endnote %}

接下来就是函数的具体实现了，所有的源代码都来自于 **《数据结构：思想与实现》** ，经历了小小的改动。以下附上源代码：

我的Github 相关代码的仓库：[GitHub - xiyuanyang-code/Data_structure](https://github.com/xiyuanyang-code/Data_structure)

```cpp
// definition

// default constructor
linkString::linkString(const char *s)
{
    // calculate the length of the string, you can just use strlen!
    length = std::strlen(s);
    nodesize = (length == 0) ? 1 : static_cast<int>(std::sqrt(length));
    node *p;
    p = head = new node(1);
    while (*s != '\0')
    {
        p = p->next = new node(nodesize);
        for (; (p->size < nodesize) && *s != '\0'; ++s, ++p->size)
        {
            p->data[p->size] = *s;
        }
    }
}

// copy constructor
linkString::linkString(const linkString &other)
{
    node *p, *otherp = other.head->next;

    // copy the new linkstring
    p = head = new node(1);
    length = other.length;
    nodesize = other.nodesize;
    while (otherp != nullptr)
    {
        p = p->next = new node(nodesize);
        for (; (p->size) < (otherp->size); ++(p->size))
        {
            // if p->size == other->size ,then all the valuable data has been copied successfully.
            p->data[p->size] = otherp->data[p->size];
        }
        otherp = otherp->next; // traverse the other link string
    }
}

/**
 * @brief clear all the memory of the linked list (private func)
 */
void linkString::clear()
{
    node *p = head->next, *nextp;
    // nextp store next pointer temporarily
    while (p != nullptr)
    {
        nextp = p->next;
        delete p;
        p = nextp;
    }
}

/**
 * @brief destructor
 */
linkString::~linkString()
{
    clear();
    delete head;
}

/**
 * @brief get the length of the string (public func)
 * @return the length of string
 */
int linkString::getlength() const
{
    return length;
}

/**
 * @brief judge whether the string is empty
 */
bool linkString::isEmpty() const
{
    return length == 0;
}

/**
 * @brief overload of assignment operator
 *
 * @param other the rvalue (const reference) of the assignment operator
 * @return the reference of the assigned lvalue
 */
linkString &linkString::operator=(const linkString &other)
{
    if (&other == this)
        return *this;

    // Clear the current list
    clear();

    // Copy the length and nodesize
    length = other.length;
    nodesize = other.nodesize;

    // Copy the nodes
    node *p = head;
    node *otherp = other.head->next;

    while (otherp != nullptr)
    {
        p->next = new node(nodesize);
        p = p->next;

        // Copy the data
        for (int i = 0; i < otherp->size; ++i)
        {
            p->data[i] = otherp->data[i];
        }
        p->size = otherp->size;

        // Move to the next node in the other list
        otherp = otherp->next;
    }

    return *this;
}

/**
 * @brief find the starting position of a given position
 *
 * @param start the given position (or index)
 * @param pos find the position in the node (reference)
 * @param p find the node where the start lies in
 */
void linkString::findPos(int start, int &pos, node *&p) const
{
    int count = 0;  // define a counter
    p = head->next; // the first node(pointer)

    while (count < start)
    {
        if (count + p->size < start)
        {
            // the start isn't in this node!
            count += p->size;
            p = p->next;
        }
        else
        {
            // the start is in this node!
            pos = start - count;
            return;
        }
    }
}

/**
 * @brief get a substring
 *
 * @param start the start position
 * @param sublength the length of the substring
 * @throws std::out_of_range If pos is out of range.
 */
linkString linkString::substr(int start, int sublength) const
{
    linkString tmp; // storing result
    int count = 0;
    int pos = 0;
    node *p, *tp = tmp.head;

    if (start < 0 || start >= length)
    {
        throw std::out_of_range("Invalid starting position!");
    }
    else
    {
        // if the length left is smaller than the given sublength:
        if (start + sublength >= length)
        {
            sublength = length - start;
        }

        tmp.length = sublength;
        tmp.nodesize = (sublength == 0) ? 1 : static_cast<int>(std::sqrt(sublength));

        // use the findPos function to find p and pos
        findPos(start, pos, p);

        // after getting the starting position, we can copy the substring!
        for (int index = 0; index < tmp.length;)
        {
            /*
             * two for-loop layers, index represents the substring index
             * inner loop is used to copy every block, while the outer loop is used to update the block.
             */
            tp = tp->next = new node(tmp.nodesize);
            while (tp->size <= tmp.nodesize && index < tmp.length)
            {
                if (pos == p->size)
                {
                    /*
                     * the traverse has ended in the block.
                     * p needs to be updated to the next block.
                     */
                    p = p->next;
                    pos = 0;
                }
                tp->data[tp->size] = p->data[pos];

                // value update
                pos++;
                index++;
                tp->size++;
            }
        }
        return tmp;
    }
}

/**
 * @brief Split operations for a block (one to two) (private func).
 *
 * @param p The node to be split.
 * @param pos The specific index where the split happens (the pos position is moved to the second node).
 * @throws std::out_of_range If pos is out of range.
 */
void linkString::split(node *p, int pos)
{
    // Check if p is nullptr
    if (p == nullptr)
    {
        throw std::invalid_argument("The node to be split is null.");
    }

    // Check if pos is within the valid range
    if (pos < 0 || pos > p->size)
    {
        throw std::out_of_range("The split position is out of range.");
    }

    // Create a new node and insert it after p
    node *nextp = new node(nodesize, p->next);
    p->next = nextp;

    // Move data from p to nextp
    for (int i = pos; i < p->size; i++)
    {
        nextp->data[i - pos] = p->data[i];
    }

    // Update sizes of both nodes
    nextp->size = p->size - pos; // Size of the new node
    p->size = pos;               // Size of the original node after split
}

/**
 * @brief merge two nodes into one
 *
 * @param p one of the nodes that need to be merged
 */
void linkString::merge(node *p)
{
    if (p == nullptr || p->next == nullptr)
        return;
    node *nextp = p->next;
    if (p->size + nextp->size <= nodesize)
    {
        // merge available
        for (int pos = 0; pos < nextp->size; ++pos, ++p->size)
        {
            p->data[p->size] = nextp->data[pos];
        }
        p->next = nextp->next;
        delete nextp;
    }
}

/**
 * @brief insert a string into linked string, using the findPos, split and merge functions
 *
 * @param start the starting position (using findPos)
 * @param other the string that needs to be inserted
 * @param insertsize the length of the string needs to be inserted, default value for all string
 */
void linkString::insert(int start, const linkString &other, int insertsize)
{
    if (insertsize == -1)
    {
        insertsize = other.length;
    }

    if (start < 0 || start > length)
    {
        throw std::out_of_range("The insert position is out of range");
    }

    if (insertsize < 0 || insertsize > other.length)
    {
        throw std::out_of_range("Invalid insertsize");
    }

    /*
     * pos and p is calculated by func findPos, representing the insert position
     * nextp is right after p, which is used for the split func
     * after the insert, the merge function will be used
     */
    node *p, *nextp, *tmp;
    int pos = 0;
    findPos(start, pos, p);
    split(p, pos);

    nextp = p->next; // nextp is used for stroage in case the link string is broken
    linkString tobeinserted = other.substr(0, insertsize);
    tmp = tobeinserted.head->next;
    while (tmp != nullptr)
    {
        // operate for every node
        for (pos = 0; pos < tmp->size; ++pos)
        {
            if (p->size == nodesize)
            {
                // need for expansion
                p = p->next = new node(nodesize);
            }
            p->data[p->size] = tmp->data[pos];
            ++p->size;
        }
        tmp = tmp->next;
    }

    length += insertsize;
    p->next = nextp;
    merge(p); // see whether the merge is available
}

/**
 * @brief Remove the specific part of a linked string
 *
 * @param deletelength the length to be deleted
 * @param start the starting position that needs tobe removed (the start itself is included), default for remove from the back
 */
void linkString::remove(int deletelength, int start)
{
    if (start == -1)
    {
        start = length - deletelength;
    }

    if (start < 0 || start >= length)
    {
        throw std::runtime_error("Invalid starting position!");
    }

    // find the position to be removed
    node *startp; // represent the starting position to be deleted
    int pos = 0;
    findPos(start, pos, startp);

    split(startp, pos); // split the node

    if (start + deletelength >= length)
    {
        deletelength = length - start;
        length = start;
        // if the deletelength goes over the edge
    }
    else
    {
        length -= deletelength;
    }

    while (true)
    {
        node *nextp = startp->next;
        if (deletelength > nextp->size)
        {
            // the end node is not here! Then delete this node!
            deletelength -= nextp->size;
            startp->next = nextp->next;
            delete nextp;
        }
        else
        {
            // the end node is here!
            split(nextp, deletelength);
            startp->next = nextp->next;
            // now the nextp is all the data needs tobe deleted, while nextp->next stores the data remaining
            delete nextp;
            break; // jump out of the loop after the remove operation is done!
        }
    }
    merge(startp); // merge all the startp;
}

/**
 * @brief Overloads the [] operator to access a character at a specific index in the linkString.
 *
 * @param index The position of the character to access (0-based index).
 * @return The character at the specified index.
 * @throws std::out_of_range If the index is invalid (less than 0 or greater than or equal to the string length).
 */
char linkString::operator[](int index) const
{
    if (index < 0 || index >= length)
    {
        throw std::out_of_range("Index " + std::to_string(index) + " is out of range. Valid range is [0, " + std::to_string(length - 1) + "].");
    }

    node *current_node;    // Pointer to the node containing the target character
    int node_position = 0; // Position within the node
    findPos(index, node_position, current_node);

    return current_node->data[node_position];
}

/**
 * @brief the overload function of += operator
 *
 * @param other the linkString needs to be appended
 * @return the reference of the appended string
 */
linkString &linkString::operator+=(const linkString &other)
{
    this->insert(length, other);
    return *this;
}

/**
 * @brief the overload function of -= operator
 *
 * @param other the linkString needs to be poped (removed)
 * @return the reference of the string
 */
linkString &linkString::operator-=(const linkString &other)
{
    linkString tmp = substr(length - other.length, other.length);
    if (tmp == other)
    {
        this->remove(length - other.length, other.length);
        return *this;
    }
    else
    {
        throw std::invalid_argument("The string cannot be removed!");
    }
}

/**
 * @brief the overload function of + operator
 *
 * @param s1 the left plus string
 * @param s2 the right plus string
 *
 * @return a tmp value of the added string
 */
linkString operator+(const linkString &s1, const linkString &s2)
{
    linkString ans = s1;
    ans += s2;
    return std::move(ans);
}

/**
 * @brief judge whether the two strings is equal
 *
 * @param s1 the left string needs to be judged
 * @param s2 the right string needs to be judged
 *
 * @return a bool value
 */
bool operator==(const linkString &s1, const linkString &s2)
{
    if (s1.length != s2.length)
        return false;
    // uses two pointers to traverse the linkString

    linkString::node *p1 = s1.head->next;
    linkString::node *p2 = s2.head->next;
    int current_pos_for_s1 = 0;
    int current_pos_for_s2 = 0;

    while (p1 && p2)
    {
        // the end statement:one of the node* reacheds to the end
        if (p1->data[current_pos_for_s1] != p2->data[current_pos_for_s2])
        {
            return false;
        }

        // update the index
        current_pos_for_s1++;
        current_pos_for_s2++;

        // update the node
        if (current_pos_for_s1 == p1->size)
        {
            p1 = p1->next;
            current_pos_for_s1 = 0;
        }

        if (current_pos_for_s2 == p2->size)
        {
            p2 = p2->next;
            current_pos_for_s2 = 0;
        }
    }
    return true;
}

/**
 * @brief judge whether the two strings are not equal
 * * All the parameters are equal to the == function
 */
bool operator!=(const linkString &s1, const linkString &s2)
{
    return !(s1 == s2);
}

// several functions for string comparison

/**
 * @brief the sorted function for string comparison, if s1 > s2, then the func returns true.
 */
bool operator>(const linkString &s1, const linkString &s2)
{
    // pointers for traverse
    linkString::node *p1 = s1.head->next;
    linkString::node *p2 = s2.head->next;
    int current_pos_for_s1 = 0;
    int current_pos_for_s2 = 0;

    while (p1 != nullptr)
    {
        if (p1->data[current_pos_for_s1] < p2->data[current_pos_for_s2])
        {
            return false;
        }

        if (p1->data[current_pos_for_s1] > p2->data[current_pos_for_s2])
        {
            return true;
        }

        // update the index
        current_pos_for_s1++;
        current_pos_for_s2++;

        // update the node
        if (current_pos_for_s1 == p1->size)
        {
            p1 = p1->next;
            current_pos_for_s1 = 0;
        }

        if (current_pos_for_s2 == p2->size)
        {
            p2 = p2->next;
            current_pos_for_s2 = 0;
        }
    }
    // whatever p2 is (nullptr or not), there is no possilbility s1>s2!
    return false;
}

bool operator>=(const linkString &s1, const linkString &s2)
{
    return s1 > s2 || s1 == s2;
}

bool operator<(const linkString &s1, const linkString &s2)
{
    return !(s1 >= s2);
}

bool operator<=(const linkString &s1, const linkString &s2)
{
    return !(s1 > s2);
}

/**
 * @brief the overload function of << operator(for output)
 *
 * @param os reference to the istream classes
 * @param s the string that needs to be printed.
 *
 * @return the l-value for os
 */
std::ostream &operator<<(std::ostream &os, const linkString &s)
{
    // for traverse
    linkString::node *p = s.head->next;

    while (p != nullptr)
    {
        for (int index = 0; index < p->size; index++)
        {
            os << p->data[index];
            // because the string doesnot have '\0', we cannot << it at a time.
        }
        p = p->next;
    }

    return os;
}

/**
 * @brief the overload function of >> operator(for input)
 *
 * @param is reference to the istream classes
 * @param s the string that needs to be printed.
 *
 * @return the l-value for is
 */
std::istream &operator>>(std::istream &is, linkString &s)
{
    const int maxinputsize = 1024;
    char *newstring = new char[maxinputsize];
    int currrentlength = 0;
    newstring[0] = '\0';

    char current_ch;
    while (is.get(current_ch) && current_ch != '\n')
    {
        newstring[currrentlength] = current_ch;
        currrentlength++;
    }
    newstring[currrentlength] = '\0';

    linkString tmp(newstring);
    s = tmp;

    delete[] newstring;
    return is;
}

/**
 * @brief the visualization of linked string
 *
 */
void linkString::visualPrint() const
{
    // for traverse
    node *current_node = head->next;
    int current_node_count = 0;

    std::cout << "The visualization for the linkString:" << std::endl;
    std::cout << "The string has " << length << "characters" << std::endl;

    while (current_node != nullptr)
    {
        std::cout << "Node " << current_node_count + 1 << ": " << std::endl;
        std::cout << "  Address: " << current_node << std::endl;
        std::cout << "  Size: " << current_node->size << std::endl;
        std::cout << "  Data: ";

        for (int i = 0; i < current_node->size; ++i)
        {
            std::cout << current_node->data[i];
        }
        std::cout << std::endl;

        std::cout << "----------------------------------------------------------------------" << std::endl;

        current_node = current_node->next;
        ++current_node_count;
    }
}
```

### String in `string`

自此，我们已经完成了 **字符串string类的几乎所有常见操作**（当然这里STL的实现还很遥远），在C++标准中，string类的相关工具函数被集成到了<string>库里，下文给出一些常见的string类的函数声明和使用方法。

```cpp
#include <string>
using namespace std;

// 构造函数
string(); // 默认构造函数，创建一个空字符串
string(const string& str); // 拷贝构造函数，创建一个与str相同的字符串
string(const char* s); // 使用C风格字符串初始化
string(size_t n, char c); // 创建一个包含n个字符c的字符串

// 赋值操作
string& operator=(const string& str); // 将str赋值给当前字符串
string& operator=(const char* s); // 将C风格字符串s赋值给当前字符串
string& operator=(char c); // 将字符c赋值给当前字符串

// 访问元素
char& operator[](size_t pos); // 返回pos位置的字符引用
const char& operator[](size_t pos) const; // 返回pos位置的字符常量引用
char& at(size_t pos); // 返回pos位置的字符引用，并进行边界检查
const char& at(size_t pos) const; // 返回pos位置的字符常量引用，并进行边界检查

// 字符串长度
size_t size() const; // 返回字符串的长度
size_t length() const; // 返回字符串的长度，与size()功能相同
bool empty() const; // 判断字符串是否为空

// 字符串操作
string& append(const string& str); // 将str追加到当前字符串末尾
string& append(const char* s); // 将C风格字符串s追加到当前字符串末尾
string& append(size_t n, char c); // 将n个字符c追加到当前字符串末尾

string& insert(size_t pos, const string& str); // 在pos位置插入str
string& insert(size_t pos, const char* s); // 在pos位置插入C风格字符串s
string& insert(size_t pos, size_t n, char c); // 在pos位置插入n个字符c

string& erase(size_t pos = 0, size_t len = npos); // 从pos位置开始删除len个字符

string& replace(size_t pos, size_t len, const string& str); // 从pos位置开始替换len个字符为str
string& replace(size_t pos, size_t len, const char* s); // 从pos位置开始替换len个字符为C风格字符串s

// 子字符串
string substr(size_t pos = 0, size_t len = npos) const; // 返回从pos位置开始长度为len的子字符串

// 查找
size_t find(const string& str, size_t pos = 0) const; // 从pos位置开始查找str，返回首次出现的位置
size_t find(const char* s, size_t pos = 0) const; // 从pos位置开始查找C风格字符串s，返回首次出现的位置
size_t find(char c, size_t pos = 0) const; // 从pos位置开始查找字符c，返回首次出现的位置

// 比较
int compare(const string& str) const; // 比较当前字符串与str，返回0表示相等，负数表示小于，正数表示大于
int compare(const char* s) const; // 比较当前字符串与C风格字符串s
```

## String Pattern Algorithms: KMP

### YOLO: You only look once!

接下来，我们介绍在字符串中非常经典的算法之一：**KMP字符串匹配算法**。

In [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science"), the **Knuth–Morris–Pratt algorithm** (or **KMP algorithm**) is a [string-searching algorithm](https://en.wikipedia.org/wiki/String-searching_algorithm "String-searching algorithm") that searches for occurrences of a "word" `W` within a main "text string" `S` by employing the observation that when a mismatch occurs, the word itself embodies sufficient information to determine where the next match could begin, thus bypassing re-examination of previously matched characters. [^1][^2]

> KMP算法中的K是Knuth老爷子！也就是图灵奖得主，《The Art of Computer Programming》巨著的作者！

**KMP算法解决的核心问题是字符串的子串匹配问题**：给定字符串`total_string` 和 `target_string`，是否存在`total_string` 的一个`substr`记为`sub_tested`，使`sub_tested` == `target_string`？如果有，返回最小的子串首字符的索引值，如果没有，返回-1。

这个问题最暴力的解决方式是**枚举**，即枚举遍`total_string`中每一种与`target_string`等长的子串，并对每一种情况作比较。

```cpp
void outermatch(seqString target, seqString totalString, int (*string_match_algorithm[])(seqString, seqString), int func_choice)
{
    int index = string_match_algorithm[func_choice](target, totalString);
    std::cout << "Using " << func_choice << "th algorithm" << std::endl;
    if (index == -1)
    {
        std::cout << "Unfortunately, the match fails." << std::endl;
        std::cout << "The string " << totalString << " does not have a substring " << target << std::endl;
    }
    else
    {
        std::cout << "Got it!" << std::endl;
        std::cout << "There exits a target string with the index of " << index << " and with the length of " << target.length() << std::endl;
        std::cout << "Which means there exists a substr: " << totalString.substr(index, target.length()) << " ,which matches with target: " << target << std::endl;
    }
}

/**
 * @brief The most complex implementation of string-matching, using enumeration
 *
 * @param target The target string needs to be matched
 * @param total_string The whole string that needs to be detected
 *
 * @return the index for the first match. return -1 if no match occurs.
 */
int complexStringMatch(seqString target, seqString totalString)
{
    int target_length = target.length();
    int total_length = totalString.length();

    if (total_length < target_length)
    {
        return false;
    }

    for (int i = 0; i + target_length <= total_length; ++i)
    {
        seqString tmp = totalString.substr(i, target_length);

        if (tmp == target)
        {
            // match successfully
            return i;
        }
    }
    return -1;
}
```

这样的算法会带来`O(mn)`的时间复杂度，m是总串的长度，n是子串的长度，在字符串长度很大时效率很低。**而KMP算法采用了时间换空间的巧妙之举**，将最坏时间复杂度压缩到了**线性级别**。

> 下面把`total_string`称为主串，`target_string`称为子串

具体是如何实现的？[^3] 类似与**移动指针**的经典算法，KMP算法首先在主串中存在一个**永远不回头的指针**，来检测所指向的字符是否和子串中的字符相对应。很显然，上文的暴力枚举算法多做了很多的重复计算，**为了减少这些重复的匹配计算**，KMP又维护了一个`next` 数组（或者叫`lps`数组），用来存储信息，这样保证了**指针可以永不回头，使用以前存储的信息**。

> 那`lps`数组储存了什么信息？**LPS 最长公共前后缀**数组的作用是：**当匹配失败时，告诉子串应该回退到哪个位置**。主串指针永不回退，子串指针根据 LPS 值跳跃。

理解起来肯定有困难，我们举一个具体的例子，例如主串ABABABABC，子串ABABC：

| 主串        | A😈 | B   | A   | B   | A   | B   | A   | B   | C           |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----------- |
| **子串**    | A😍 | B   | A   | B   | C   |     |     |     |             |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **forward** |

我们假设lps数组已经生成好了，现在有两个指针（都位于初始位置起点：😈😍）

> - 😈：**位于主串的永不后退的指针**，代表在主串中扫描到的位置。
> 
> - 😍：**位于子串的指针**，不过他可能会后退
> 
> 两个指针指向的字符会进行匹配操作，判断是否对应。

接下来字符进行**第一次匹配**：A == A，匹配成功，**两个指针同时向前移动一步进行后续匹配**。

接着就是第二步，第三步，第四步，匹配都成功。

| 主串        | A   | B😈 | A   | B   | A   | B   | A   | B   | C           |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----------- |
| **子串**    | A   | B😍 | A   | B   | C   |     |     |     |             |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **forward** |

| 主串        | A   | B   | A😈 | B   | A   | B   | A   | B   | C           |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----------- |
| **子串**    | A   | B   | A😍 | B   | C   |     |     |     |             |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **forward** |

| 主串        | A   | B   | A   | B😈 | A   | B   | A   | B   | C           |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----------- |
| **子串**    | A   | B   | A   | B😍 | C   |     |     |     |             |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **forward** |

第五步开始了，出现了异常情况！字符串匹配失败。

| 主串        | A   | B   | A   | B   | A😈 | B   | A   | B   | C           |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----------- |
| **子串**    | A   | B   | A   | B   | C😇 |     |     |     |             |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **failure** |

别慌！**虽然匹配失败了，但我们并不是一无所获**。我们至少发现前四位的匹配是有效的，我们如何利用这个有效信息？

注意力惊人的KMP三人发现，我们可以让子串的😇指针**适当的回退几步**。注意是**适当**，也就是说没有必要回退到第一位重新开始。那回退到哪一步呢？在这里，我们可以**将😇指针回退两步，看看会发生什么**：

| 主串        | A                                 | B                                 | <span style="color:red;">A</span>   | <span style="color:red;">B</span> | <span style="color:red;">A</span>😈 | B   | A   | B   | C             |
| --------- | --------------------------------- | --------------------------------- | ----------------------------------- | --------------------------------- | ----------------------------------- | --- | --- | --- | ------------- |
| **子串**    | <span style="color:red;">A</span> | <span style="color:red;">B</span> | <span style="color:red;">A😍</span> | B                                 | C                                   |     |     |     |               |
| **lps数组** | 0                                 | 0                                 | 1                                   | 2                                 | 0                                   |     |     |     | **<- 2steps** |

神奇的事情发生了！我们发现貌似子串的前三位是对应上的，貌似还有希望！此时😈😍已经错开了位置，但依旧可以同时向后移动，进行比较。**换句话说，主串的指针无需向后移动，只需子串的😇向后移动适当的位置，便可重新“错位”对接上，进行后续的移动操作。**

有了这个基本思路，我们便可以走完剩下的扫描过程：

> 为了方便演示，我们使用😀标注主串和子串中正在匹配字符串的开头位置，这个本身没有任何意义，只是为了更加的直观。

| 主串        | A   | B   | A😀 | B   | A   | B😈 | A   | B   | C           |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----------- |
| **子串**    | A😀 | B   | A   | B😍 | C   |     |     |     |             |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **success** |

| 主串        | A   | B   | A😀 | B   | A   | B   | A😈 | B   | C     |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----- |
| **子串**    | A😀 | B   | A   | B   | C😇 |     |     |     |       |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | fuck！ |

| 主串        | A   | B   | A   | B   | A😀 | B   | A😈 | B   | C             |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ------------- |
| **子串**    | A😀 | B   | A😍 | B   | C   |     |     |     |               |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | **<- 2steps** |

| 主串        | A   | B   | A   | B   | A😀 | B   | A   | B   | C😈   |
| --------- | --- | --- | --- | --- | --- | --- | --- | --- | ----- |
| **子串**    | A😀 | B   | A   | B   | C😍 |     |     |     |       |
| **lps数组** | 0   | 0   | 1   | 2   | 0   |     |     |     | yeah！ |

我们最终找到了匹配的字符串！

不过有两个问题还未解决：

**第一个问题是：我如何知道在匹配失败的时候应该回退到哪个位置？**

我们再来仔细看看回退的时候到底发生了什么，以第一次回退的情况为例：

| 主串        | <span style="color:blue;">A</span> | <span style="color:blue;">B</span> | <span style="color:red;">A</span>     | <span style="color:red;">B</span> | <span style="color:black;">A</span>😈 | B   | A   | B   | C             |
| --------- | ---------------------------------- | ---------------------------------- | ------------------------------------- | --------------------------------- | ------------------------------------- | --- | --- | --- | ------------- |
| **子串**    | <span style="color:red;">A</span>  | <span style="color:red;">B</span>  | <span style="color:black;">A😍</span> | B<-                               | C<-                                   |     |     |     |               |
| **lps数组** | 0                                  | 0                                  | 1                                     | 2                                 | 0                                     |     |     |     | **<- 2steps** |

在主串中，本应该是蓝色的AB和子串中红色的AB进行匹配（实际上也确实如此），但是我们巧妙的发现**主串尾部也有相同的AB序列**，这就给我们一种**错位**的机会，即**两个字符串中红色和红色**相匹配的机会。

换句话说，**我们真正关心的**，是之前已经匹配好的子串中**最长的相同前后缀**（在上文的情况中是AB），也就是最大的错位长度可能性。

**第二个问题是：电脑如何知道匹配失败的时候应该回退到哪个位置？**

答案：**查表**，第三行的lps数组就是答案。当😇位于子串第五个位置的C处时，会peek一下第四个位置（最后一个匹配成功的位置）的lps数组值，发现是2，因此子串的指针回退2格。

在**假设lps数组已经给出的条件下，我们可以完成KMP算法的大部分内容了！**

```cpp
// KMP algorithm implementation
int KMP(seqString pattern, seqString text) {
    if (pattern.isEmpty()) return 0;  // Empty pattern matches at the start

    int textLen = text.length();
    int patLen = pattern.length();
    std::vector<int> lps(patLen, 0);
    computeLPS(pattern, lps);

    int i = 0;  // Index for text
    int j = 0;  // Index for pattern

    while (i < textLen) {
        if (pattern[j] == text[i]) {
            i++;
            j++;
        }

        if (j == patLen) {
            return i - j;  // Match found, return the starting index
        } else if (i < textLen && pattern[j] != text[i]) {
            if (j != 0) {
                j = lps[j - 1];  // Fallback using the LPS array
            } else {
                i++;  // No fallback possible, move to the next character in text
            }
        }
    }

    return -1;  // No match found
}
```

在代码中的`computeLPS()`函数完成了lps数组的计算工作，稍后完成。

### How to compute `LPS`

最后一个问题，如何计算LPS数组？我们知道LPS数组的本质就是**计算子串中最长的相同前后缀长度**，也就是说LPS的计算和主串无关，而我们在扫描的过程中肯定会完整的遍历一遍子串，因此可以在扫描的过程中同步更新LPS数组，不过在这里为了方便写封装，我们在开始之前提前计算好LPS数组。

`computeLPS()`如何也实现线性时间复杂度？**动态规划**，我们首先来规范的定义LPS数组的求解问题：

给定一个vector<int> 数组lps，计算`lps[i]`，需要考虑子串的`substr(0, i-1)`，判断其最大的公共前后缀长度，即为`lps[i]`的值。

这可以使用**动态规划**的方法解决，高效地利用之前得到的知识。

| 子串  | A   | B   | A   | C   | A   | B😍 | B   | C   |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

- 对应 index == 0，无法回退，为0。

- 对于 index == 1，考虑字符串A，为1。

- index == 2，我们发现新加入的B和A并不相同，为0

- index == 3，为3

- index == 4，为0

- index == 6，😍，增加一个字符lps的值至多增加1，这**取决于新加入的字符和原来最长前缀的下一个字符是否匹配**，如果匹配，lps数组加1.
  
  ```cpp
  if(newaddedchar == target_string[lps[index - 1] + 1){
      lps[index] = lps[index - 1] + 1;
  }
  ```

对于无法匹配的情况，例如index == 7，情况貌似有点遭，因为新加入的B不仅没对上，还打破了ABACAB原有的结构，变成了**AB**AC**AB**B，此时我们需要找到更短的前后缀，我们换一个例子：

**ABDA**AC**ABDA**+B。我们寻找更短的公共前后缀，可以暴力搜索，但也可以巧妙的剪枝：**本质上就是寻找ABDA中最长的公共前后缀**，再重复上面的过程，而这一步可以通过查表得到。

综上所述，我们可以给出`computeLPS()`的完整代码

```cpp
void computeLPS(const seqString& pattern, std::vector<int>& lps) {
    int len = 0;  // Length of the previous longest prefix suffix
    lps[0] = 0;   // lps[0] is always 0
    int i = 1;

    while (i < pattern.length()) {
        if (pattern[i] == pattern[len]) {
            len++;
            lps[i] = len;
            i++;
        } else {
            if (len != 0) {
                len = lps[len - 1];  // Fallback to the previous longest prefix suffix
                //特别注意：此处并未执行i++，因为这个位置的值还没有确定，需要再更新
            } else {
                lps[i] = 0;  // No matching prefix suffix
                i++;
            }
        }
    }
}
```

最后，我们给出一个使用KMP算法的实例：

```cpp
/*
 *The following code is for string-match problems
 *We use our handmade string for sequence implementation
 */
// declaration
void outermatch(seqString target, seqString totalString, int (*string_match_algorithm[])(seqString, seqString), int func_choice);
int complexStringMatch(seqString target, seqString totalString);
int KMP(seqString target, seqString totalString);
void computeLPS(const seqString &pattern, std::vector<int> &next);

/**
 * @brief The outer message output of string-matching problems
 *
 * @param target The target string needs to be matched
 * @param total_string The whole string that needs to be detected
 * @param string_match_algorithm The specific string-match problems
 * @param func_choice Choose one algorithm
 */
void outermatch(seqString target, seqString totalString, int (*string_match_algorithm[])(seqString, seqString), int func_choice)
{
    int index = string_match_algorithm[func_choice](target, totalString);
    std::cout << "Using " << func_choice << "th algorithm" << std::endl;
    if (index == -1)
    {
        std::cout << "Unfortunately, the match fails." << std::endl;
        std::cout << "The string " << totalString << " does not have a substring " << target << std::endl;
    }
    else
    {
        std::cout << "Got it!" << std::endl;
        std::cout << "There exits a target string with the index of " << index << " and with the length of " << target.length() << std::endl;
        std::cout << "Which means there exists a substr: " << totalString.substr(index, target.length()) << " ,which matches with target: " << target << std::endl;
    }
}


/**
 * !KMP algorithm
 * @brief  the Knuth-Morris-Pratt (KMP) algorithm
 *
 * @param text The main text string to search within
 * @param pattern The pattern string to search for
 * @return The first occurrence index of the pattern in the text, or -1 if not found
 */
void computeLPS(const seqString& pattern, std::vector<int>& lps) {
    int len = 0;  // Length of the previous longest prefix suffix
    lps[0] = 0;   // lps[0] is always 0
    int i = 1;

    while (i < pattern.length()) {
        if (pattern[i] == pattern[len]) {
            len++;
            lps[i] = len;
            i++;
        } else {
            if (len != 0) {
                len = lps[len - 1];  // Fallback to the previous longest prefix suffix
            } else {
                lps[i] = 0;  // No matching prefix suffix
                i++;
            }
        }
    }
}

// KMP algorithm implementation
int KMP(seqString pattern, seqString text) {
    if (pattern.isEmpty()) return 0;  // Empty pattern matches at the start

    int textLen = text.length();
    int patLen = pattern.length();
    std::vector<int> lps(patLen, 0);
    computeLPS(pattern, lps);

    int i = 0;  // Index for text
    int j = 0;  // Index for pattern

    while (i < textLen) {
        if (pattern[j] == text[i]) {
            i++;
            j++;
        }

        if (j == patLen) {
            return i - j;  // Match found, return the starting index
        } else if (i < textLen && pattern[j] != text[i]) {
            if (j != 0) {
                j -= lps[j - 1];  // Fallback using the LPS array
            } else {
                i++;  // No fallback possible, move to the next character in text
            }
        }
    }
    return -1;  // No match found
}


void testPackageForKMP()
{
    seqString s1("Hello my name is YXY and I love learning DS");
    seqString targets[] = {"Hello", "my", "MM"};
    int (*string_match_algorithm[])(seqString, seqString) = {complexStringMatch, KMP};

    for (int i = 0; i < 2; i++)
    {
        for (auto target : targets)
        {
            outermatch(target, s1, string_match_algorithm, i);
        }
    }
}
```

{% note primary %}

**KMP**算法的本质也可以看做**对暴力枚举的剪枝操作**，因为如果不满足相同前后缀的条件的话，所选择的子串一定是匹配不上的。LPS数组记录了子串的“自相似性”（即前缀和后缀的重叠部分），而KMP算法通过LPS数组，直接跳过那些不可能匹配的位置，只检查可能匹配的部分。

{% endnote %}

### Regex

KMP算法极大程度的压缩了字符串匹配的时间，但**在实际生活中我们往往需要更加general的字符串匹配**来面对更加刁难的需求，例如我需要找到一个子串，要求这个子串的格式为`ILOVEXXX`，后三位可以是任何的大写字母，那这时我们应该如何表达这个字符串？如何设计功能更加强大的字符串匹配算法？或许**正则表达式Regex**可以成为解决问题的法宝。

笔者专门更新了一片博客讲解Regex的语法：[Xiyuan Yang's Blog - Regex](https://xiyuanyang-code.github.io/posts/Regular-Expression/)

## References

[^1]: [Knuth–Morris–Pratt algorithm - Wikipedia](https://en.wikipedia.org/wiki/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm)

[^2]: [Papers of KMP algorithm - www.cs.jhu.edu](https://www.cs.jhu.edu/~misha/ReadingSeminar/Papers/Knuth77.pdf)

[^3]: [KMP算法可视化 奇乐编程学院](https://www.bilibili.com/video/BV1AY4y157yL/)
