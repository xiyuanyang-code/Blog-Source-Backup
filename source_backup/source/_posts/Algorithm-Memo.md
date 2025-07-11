---
title: Algorithm-Memo
date: 2025-03-23 14:10:02
index_img: /img/cover/stackinc.jpg
excerpt: Memo for several frequently used algorithms and data sturctures.
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Algorithm Memo

## Several STL functions

### Read Fast!

Close the synchronous stream if you want to use `cin/cout`.

or just simply use the `read()` func:

```cpp
inline int read() {
    int x = 0, f = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9') {
        if (ch == '-') f = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') x = x * 10 + ch - '0', ch = getchar();
    return x * f;
}
```

Remember to change this if the number is very large:

```cpp
inline long long read() {
    long long x = 0, f = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9') {
        if (ch == '-') f = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9') x = x * 10 + ch - '0', ch = getchar();
    return x * f;
}
```

And you should use `cout << '\n';` instead of `cout << endl;`!

### Sort very quickly

```cpp
#include <iostream>
#include <cstdlib> // 用于 std::rand 和 std::srand
#include <ctime>   // 用于 std::time

// 模板函数：快速排序的核心函数，用于分区
template <typename T>
long long partition(T* arr, long long low, long long high) {
    // 随机选择基准值
    long long random_pivot_index = low + rand() % (high - low + 1);
    swap(arr[low], arr[random_pivot_index]); // 将随机选择的基准值放到第一个位置

    // 正常的分区逻辑
    T pivot = arr[low];
    long long i = low + 1;  // i 指向当前处理的元素
    long long j = high;     // j 指向末尾元素

    while (true) {
        // 向右移动 i，直到找到大于 pivot 的元素
        while (i <= j && arr[i] <= pivot) {
            i++;
        }
        // 向左移动 j，直到找到小于等于 pivot 的元素
        while (i <= j && arr[j] > pivot) {
            j--;
        }
        // 如果 i 和 j 交错，则停止循环
        if (i >= j) {
            break;
        }
        // 交换 arr[i] 和 arr[j]
        swap(arr[i], arr[j]);
    }
    // 将基准值放到正确的位置
    swap(arr[low], arr[j]);
    return j; // 返回基准值的最终位置
}

// 模板函数：快速排序的递归实现
template <typename T>
void quick_sort(T* arr, long long low, long long high) {
    if (low < high) {
        // 获取分区点
        long long pivot_index = partition(arr, low, high);
        // 对左子数组进行快速排序
        quick_sort(arr, low, pivot_index - 1);
        // 对右子数组进行快速排序
        quick_sort(arr, pivot_index + 1, high);
    }
}
```

Remember to use `srand(time(0)); ` in your `main` function!

## Several STL containers

### Vector

```cpp
#ifndef SJTU_VECTOR_HPP
#define SJTU_VECTOR_HPP

#include "exceptions.hpp"
#include <climits>
#include <cstddef>
#include <cmath>
#include <cmath>
#include <cstring>
#include <memory>

namespace sjtu {

/**
 * a data container like std::vector
 * store data in a successive memory and support random access.
 */
template<typename T>
class vector {
private:
    T *data; // for data storage
    size_t current_size; // the size of vector
    size_t max_size; // default value is 16

    /**
     * @brief expand the memory (the maxsize and data) to its double size
     * 
     * just expand the memory without allocating new members
     */
    void expandDouble() {
        std::allocator<T> alloc;
        size_t new_max_size = max_size * 2;
        T* new_data = alloc.allocate(new_max_size);

        // move to the new memory space
        for (size_t i = 0; i < current_size; ++i) {
            std::construct_at(new_data + i, std::move(data[i]));
            std::destroy_at(data + i);
        }

        // release old memory
        if (data) {
            alloc.deallocate(data, max_size);
        }

        // update the pointer
        data = new_data;
        new_data = nullptr;
        max_size = new_max_size;
    }

public:
    /**
     * @brief Tool function, delete all the elements but does not free the memory
     */
    void clear() {
        std::allocator<T> alloc;
        for (size_t i = 0; i < current_size; ++i) {
            std::destroy_at(data + i);
        }
        current_size = 0;
    }

public:
    class const_iterator;

    class iterator {
        // The following code is written for the C++ type_traits library.
        // Type traits is a C++ feature for describing certain properties of a type.
        // For instance, for an iterator, iterator::value_type is the type that the
        // iterator points to.
        // STL algorithms and containers may use these type_traits (e.g. the following
        // typedef) to work properly. In particular, without the following code,
        // @code{std::sort(iter, iter1);} would not compile.
        // See these websites for more information:
        // https://en.cppreference.com/w/cpp/header/type_traits
        // About value_type: https://blog.csdn.net/u014299153/article/details/72419713
        // About iterator_category: https://en.cppreference.com/w/cpp/iterator
    public:
        using difference_type = std::ptrdiff_t;
        using value_type = T;
        using pointer = T*;
        using reference = T&;
        using iterator_category = std::output_iterator_tag;

    private:
        T* ptr; // the pointer
        vector<T>* which_vector; // judge whether two pointers are pointing to the same vector

    public:
        // constructor
        iterator(T* ptr_, vector<T>* which_vec) : ptr(ptr_), which_vector(which_vec) {}

        iterator operator+(const int& n) const {
            iterator tmp = iterator(ptr + n, this->which_vector);
            if (tmp < (*which_vector).begin() || (*which_vector).end() < tmp) {
                throw invalid_iterator();
            }
            return tmp;
        }

        iterator operator-(const int& n) const {
            iterator tmp = iterator(ptr - n, this->which_vector);
            if (tmp < (*which_vector).begin() || (*which_vector).end() < tmp) {
                throw invalid_iterator();
            }
            return tmp;
        }

        // return the distance between two iterators,
        // if these two iterators point to different vectors, throw invalid_iterator.
        int operator-(const iterator& rhs) const {
            if (which_vector != rhs.which_vector) {
                // two iterators point to different vectors!
                throw invalid_iterator();
            }
            return ptr - rhs.ptr;
        }

        /**
         * @brief let the iterator to move towards n steps
         * 
         * @param n 
         * @return iterator& 
         */
        iterator& operator+=(const int& n) {
            ptr += n; // movement
            if (*this < (*which_vector).begin() || (*which_vector).end() < *this) {
                throw invalid_iterator();
            }
            return *this;
        }

        iterator& operator-=(const int& n) {
            ptr = ptr - n;
            if (*this < (*which_vector).begin() || (*which_vector).end() < *this) {
                throw invalid_iterator();
            }
            return *this;
        }

        iterator operator++(int) {
            iterator temp = *this;
            ++ptr;
            return temp;
        }

        iterator& operator++() {
            ++ptr;
            if (*this < (*which_vector).begin() || (*which_vector).end() < *this) {
                throw invalid_iterator();
            }
            return *this;
        }

        iterator operator--(int) {
            iterator tmp = *this;
            --ptr;
            return tmp;
        }

        iterator operator--() {
            --ptr;
            if (*this < (*which_vector).begin() || (*which_vector).end() < *this) {
                throw invalid_iterator();
            }
            return *this;
        }

        T& operator*() const {
            return *ptr;
        }

        bool operator==(const iterator& rhs) const {
            return (which_vector == rhs.which_vector) && (ptr == rhs.ptr);
        }

        bool operator==(const const_iterator& rhs) const {
            return (which_vector == rhs.which_vector) && (ptr == rhs.ptr);
        }

        bool operator!=(const iterator& rhs) const {
            return !(*this == rhs);
        }

        bool operator!=(const const_iterator& rhs) const {
            return !(*this == rhs);
        }

        bool operator<(const iterator& rhs) const {
            return (*this - rhs) < 0;
        }
    };

    class const_iterator {
    public:
        using difference_type = std::ptrdiff_t;
        using value_type = T;
        using pointer = T*;
        using reference = T&;
        using iterator_category = std::output_iterator_tag;

    private:
        const T* ptr;
        const vector<T>* which_vector;

    public:
        const_iterator(const T* p, const vector<T>* which_vec) : ptr(p), which_vector(which_vec) {}

        const_iterator operator+(const int n) const {
            return const_iterator(ptr + n);
        }

        const_iterator operator-(const int n) const {
            return const_iterator(ptr - n);
        }

        int operator-(const const_iterator& rhs) const {
            if (which_vector != rhs.which_vector) {
                throw invalid_iterator();
            } else {
                return ptr - rhs.ptr;
            }
        }

        const_iterator& operator+=(const int& n) {
            ptr = ptr + n;
            return *this;
        }

        const_iterator& operator-=(const int& n) {
            ptr = ptr - n;
            return *this;
        }

        const_iterator operator++(int) {
            const_iterator temp = *this;
            ++ptr;
            return temp;
        }

        const_iterator& operator++() {
            ++ptr;
            return *this;
        }

        const_iterator operator--() {
            --ptr;
            return *this;
        }

        const_iterator operator--(int) {
            const_iterator tmp = *this;
            --ptr;
            return tmp;
        }

        T operator*() const {
            return *ptr;
        }

        bool operator==(const const_iterator& rhs) const {
            return ptr == rhs.ptr;
        }

        bool operator==(const iterator& rhs) const {
            return ptr == rhs.ptr;
        }

        bool operator!=(const iterator& rhs) const {
            return !(*this == rhs);
        }

        bool operator!=(const const_iterator& rhs) const {
            return !(*this == rhs);
        }
    };

    /**
     * @brief Construct a new vector object (default)
     */
    vector() : current_size(0), max_size(16) {
        // default
        std::allocator<T> alloc;
        data = alloc.allocate(max_size);
    }

    /**
     * @brief Construct a new vector object
     * 
     * @param size_
     */
    vector(size_t size_) {
        current_size = 0;
        max_size = (current_size < 16) ? 16 : current_size;
        std::allocator<T> alloc;
        data = alloc.allocate(max_size);
    }

    vector(const vector& other) {
        current_size = other.current_size;
        max_size = other.max_size;
        std::allocator<T> alloc;
        data = alloc.allocate(max_size);
        for (int i = 0; i < current_size; ++i) {
            std::construct_at(data + i, other.data[i]);
        }
    }

    ~vector() {
        clear(); // destroy all data, not release memory
        if (data) {
            std::allocator<T> alloc;
            alloc.deallocate(data, max_size);
        }
    }

    /**
     * @brief assignment operator
     * 
     * @param other 
     * @return vector& the assigned operator
     */
    vector<T>& operator=(const vector& other) {
        if (this != &other) {
            clear();
            std::allocator<T> alloc;
            if (data) {
                alloc.deallocate(data, max_size);
            }
            data = alloc.allocate(other.max_size);
            max_size = other.max_size;
            current_size = other.current_size;
            for (size_t i = 0; i < current_size; ++i) {
                std::construct_at(data + i, other.data[i]);
            }
        }
        return *this;
    }

    /**
     * assigns specified element with bounds checking
     * throw index_out_of_bound if pos is not in [0, size)
     */
    T& at(const size_t& pos) {
        if (pos < 0 || pos >= current_size) {
            throw index_out_of_bound();
        } else {
            return data[pos];
        }
    }

    const T& at(const size_t& pos) const {
        if (pos < 0 || pos >= current_size) {
            throw index_out_of_bound();
        } else {
            return data[pos];
        }
    }

    /**
     * assigns specified element with bounds checking
     * throw index_out_of_bound if pos is not in [0, size)
     * !!! Pay attentions
     *   In STL this operator does not check the boundary but I want you to do.
     */
    T& operator[](const size_t& pos) {
        if (pos < 0 && pos >= current_size) {
            throw index_out_of_bound();
        } else {
            return data[pos];
        }
    }

    const T& operator[](const size_t& pos) const {
        if (pos < 0 && pos >= current_size) {
            throw index_out_of_bound();
        } else {
            return data[pos];
        }
    }

    /**
     * access the first element.
     * throw container_is_empty if size == 0
     */
    const T& front() const {
        if (current_size == 0) {
            throw container_is_empty();
        } else {
            return data[0];
        }
    }

    /**
     * access the last element.
     * throw container_is_empty if size == 0
     */
    const T& back() const {
        if (current_size == 0) {
            throw container_is_empty();
        } else {
            return data[current_size - 1];
        }
    }

    /**
     * returns an iterator to the beginning.
     */
    iterator begin() {
        if (empty()) {
            throw container_is_empty();
        }
        iterator tmp(data, this);
        return tmp;
    }

    const_iterator begin() const {
        if (empty()) {
            throw container_is_empty();
        } else {
            const_iterator tmp(data, this);
            return tmp;
        }
    }

    const_iterator cbegin() const {
        if (empty()) {
            throw container_is_empty();
        } else {
            const_iterator tmp(data, this);
            return tmp;
        }
    }

    /**
     * returns an iterator to the end.
     */
    iterator end() {
        if (empty()) {
            throw container_is_empty();
        } else {
            iterator tmp(data + current_size, this);
            return tmp;
        }
    }

    const_iterator end() const {
        if (empty()) {
            throw container_is_empty();
        } else {
            const_iterator tmp(data + current_size, this);
            return tmp;
        }
    }

    const_iterator cend() const {
        if (empty()) {
            throw container_is_empty();
        } else {
            const_iterator tmp(data + current_size, this);
            return tmp;
        }
    }

    /**
     * checks whether the container is empty
     */
    bool empty() const {
        return current_size == 0;
    }

    /**
     * returns the number of elements
     */
    size_t size() const {
        return current_size;
    }

    /**
     * inserts value before pos
     * returns an iterator pointing to the inserted value.
     */
    iterator insert(iterator pos, const T& value) {
        // getting the inserted index
        const size_t index = pos - (this->begin());
        return insert(index, value);
    }

    /**
     * inserts value at index ind.
     * after inserting, this->at(ind) == value
     * returns an iterator pointing to the inserted value.
     * throw index_out_of_bound if ind > size (in this situation ind can be size because after inserting the size will increase 1.)
     */
    iterator insert(const size_t& pos, const T& value) {
        //check if it is valid
        if (pos > current_size) {
            throw index_out_of_bound();
        }
        //expand memory
        if (current_size == max_size) {
            expandDouble();
        }
        std::construct_at(data + current_size, value);

        //move backwards
        for (size_t i = current_size; i > pos; --i) {
            data[i] = std::move(data[i - 1]);
        }

        //create a new element
        data[pos] = value;
        ++current_size;

        return begin() + pos;
    }

    /**
     * removes the element at pos.
     * return an iterator pointing to the following element.
     * If the iterator pos refers the last element, the end() iterator is returned.
     */
    iterator erase(iterator pos) {
        if (pos == end()) {
            // like the push back
            return end();
        }
        iterator next_pos = pos + 1;
        std::move(next_pos, end(), pos);

        // update the vector
        std::destroy_at(data + current_size - 1);
        --current_size;
        return pos;
    }

    /**
     * removes the element with index ind.
     * return an iterator pointing to the following element.
     * throw index_out_of_bound if ind >= size
     */
    iterator erase(const size_t& ind) {
        if (ind >= current_size) {
            throw index_out_of_bound();
        } else {
            std::move(data + ind + 1, data + current_size, data + ind);
            std::destroy_at(data + current_size - 1);
            --current_size;
            return this->begin() + ind;
        }
    }

    /**
     * adds an element to the end.
     */
    void push_back(const T& value) {
        if (current_size == max_size) {
            // needs to expand
            expandDouble();
        }
        std::construct_at(data + current_size, value);
        ++current_size;
    }

    /**
     * remove the last element from the end.
     * throw container_is_empty if size() == 0
     */
    void pop_back() {
        if (size() == 0) {
            throw container_is_empty();
        } else {
            std::destroy_at(data + current_size - 1);
            --current_size;
        }
    }
};

} // namespace sjtu

#endif
```

### Queue

```cpp
// 顺序循环队列类的定义及基本操作实现

#ifndef SEQQUEUE_H_INCLUDED
#define SEQQUEUE_H_INCLUDED

class illegalSize{};
class outOfBound{};

template <class elemType>
class seqQueue
{    
    private:
        elemType *array;
        int maxSize;
        int Front, Rear;
        void doubleSpace(); //扩展队队列元素的存储空间为原来的2倍
    public:
        seqQueue(int size=10); //初始化队列元素的存储空间
        bool isEmpty(); //判断队空否，空返回true，否则为false
        bool isFull();  //判断队满否，满返回true，否则为false
        elemType front(); //读取队首元素的值，队首不变
        void enQueue(const elemType &x); //将x进队，成为新的队尾
        void deQueue(); //将队首元素出队
        ~seqQueue(); //释放队列元素所占据的动态数组
};

template <class elemType>
seqQueue<elemType>::seqQueue(int size) //初始化队列元素的存储空间
{
    array = new elemType[size]; //申请实际的队列存储空间

    if (!array) throw illegalSize();

    maxSize = size;
    Front = Rear = 0;
}

template<class elemType>
bool seqQueue<elemType>::isEmpty() //判断队空否，空返回 true,否则为false
{
    return Front == Rear;
}

template <class elemType>
bool seqQueue<elemType>::isFull() //判断队满否，满返回 true,否则为false
{
    return (Rear+1)%maxSize == Front;
}

template <class elemType>
elemType seqQueue<elemType>::front() //读取队首元素的值，队首不变
{
    if (isEmpty()) throw outOfBound(); //先判断

    return array[Front]; 
}

template <class elemType>
void seqQueue<elemType>::enQueue(const elemType &x)  //将x进队，成为新的队尾
{  
    if (isFull()) doubleSpace();

    array[Rear] = x;
    Rear = (Rear+1)%maxSize;
}

template <class elemType>
void seqQueue<elemType>::deQueue() //将队首元素出队
{  
    if (isEmpty()) throw outOfBound();

    Front = (Front+1)%maxSize;
}

template <class elemType>
seqQueue<elemType>::~seqQueue() //释放队列元素所占据的动态数组
{ 
    delete []array;
}

template <class elemType>
void seqQueue<elemType>::doubleSpace() //扩展队列元素的存储空间为原来的2倍
{
    elemType* newArray;
    int i, j;

    newArray = new elemType[2*maxSize];
    if (!newArray) throw illegalSize();

    for (i = 0, j = Front; j != Rear; i++, j = (j+1)%maxSize) //直接重排
        newArray[i] = array[j];

    delete []array; //释放原来的小空间

    array = newArray;
    Front = 0;
    Rear = i;
    maxSize = 2*maxSize;
}

#endif
```

### Stack

```cpp
// 顺序栈结构定义及基本操作的实现

// 顺序栈的描述：数组指针array，数组大小maxSize，栈顶下标Top
class illegalSize{};
class outOfBound{};

template <class elemType>
class seqStack
{  
    private:
        elemType *array;    //栈存储数组，存放实际的数据元素。
        int Top;            //栈顶下标。
        int maxSize;	    //栈中最多能存放的元素个数。
        void doubleSpace();
    public:
        seqStack(int initSize = 100); //初始化顺序栈
        bool isEmpty(){ return (Top == -1 ); } ; //栈空返回true,否则返回false
        bool isFull(){ return (Top == maxSize-1); }; //栈满返回true,否则返回false
        elemType top(); //返回栈顶元素的值，不改变栈顶
        void push(const elemType &e); //将元素e压入栈顶，使其成为新的栈顶
        void pop(); //将栈顶元素弹栈
        ~seqStack(){ delete []array; }; //释放栈占用的动态数组
};

template <class elemType>
seqStack<elemType>::seqStack(int initSize)//初始化顺序栈
{	
    array = new elemType[initSize];

	if (!array) throw illegalSize();

	Top = -1;    
    maxSize = initSize;
}

template <class elemType>
void seqStack<elemType>::doubleSpace() //分配2倍的空间
{
    elemType *tmp;
    int i;

    tmp = new elemType[maxSize*2];

    if (!tmp) throw illegalSize();

    for (i = 0; i <= Top; i++)
        tmp[i] = array[i];

    delete []array;
    array = tmp;
    maxSize = 2*maxSize;
}

template <class elemType>
elemType seqStack<elemType>::top () //返回栈顶元素的值，不改变栈顶
{   
    if (isEmpty()) throw outOfBound();

    return array[Top];
}

template <class elemType>
void seqStack<elemType>::push(const elemType &e )
//将元素e压入栈顶，使其成为新的栈顶元素
{     
    if (isFull()) doubleSpace(); //栈满时重新分配2倍的空间，并将原空间内容拷入
      
    array[++Top] = e; // 新结点放入新的栈顶位置。
}

template <class elemType>
void seqStack<elemType>::pop() //将栈顶元素弹栈
{  
    if (Top == -1) throw outOfBound();

    Top--; //不管此元素
}

// 函数initialize(seqStack)、isEmpty、isFull、top、pop、destroy（~seqStack）的时间复杂度均为O(1)
// push因某时可能扩大空间，造成O(n)时间消耗，但按照“分期付款式”法，分摊到单次的插入操作，时间复杂度仍为O(1)
```

## Disjoint Set

```cpp
#include <cstddef>
#include <iostream>

class disjointSet {
private:
    size_t size;
    int *parent;

public:
    /**
     * @brief Construct a new disjoint Set object
     * 
     * @param s 
     */
    disjointSet(size_t s) : size(s) {
        parent = new int[size];

        // initiallize
        for (int i = 0; i < size; i++) {
            parent[i] = -1;
        }
    }

    /**
     * @brief Destroy the disjoint Set object
     * 
     */
    ~disjointSet() {
        delete parent;
    }

    /**
     * @brief Union two subtree, merged by scale
     * 
     * @param root1 
     * @param root2 
     */
    void Union(int root1, int root2) {
        if (root1 == root2) {
            return;
        }

        // !attention, these are all negative
        if (parent[root1] > parent[root2]) {
            parent[root2] += parent[root1];
            parent[root1] = root2;
        } else {
            parent[root1] += parent[root2];
            parent[root2] = root1;
        }
    }

    /**
     * @brief find the set which x lies in, using recursion
     * 
     * @param x 
     * @return int 
     */
    int Find(int x) {
        if (parent[x] < 0) {
            // x is the root node
            return x;
        }

        // optimize
        return (parent[x] = Find(parent[x]));
    }
};

int main() {
    // Create a disjoint set with 10 elements (0 to 9)
    disjointSet ds(10);

    // Perform some union operations
    std::cout << "Performing union operations..." << std::endl;
    ds.Union(0, 1);
    ds.Union(2, 3);
    ds.Union(4, 5);
    ds.Union(6, 7);
    ds.Union(8, 9);
    ds.Union(1, 3);// Union of sets containing 0, 1 and 2, 3
    ds.Union(5, 7);// Union of sets containing 4, 5 and 6, 7

    // Check the root of each element
    std::cout << "\nFinding roots of elements..." << std::endl;
    for (int i = 0; i < 10; i++) {
        std::cout << "Element " << i << " belongs to set with root: " << ds.Find(i) << std::endl;
    }

    // Check if two elements are in the same set
    std::cout << "\nChecking if elements are in the same set..." << std::endl;
    std::cout << "Are 0 and 3 in the same set? " << (ds.Find(0) == ds.Find(3) ? "Yes" : "No") << std::endl;
    std::cout << "Are 4 and 7 in the same set? " << (ds.Find(4) == ds.Find(7) ? "Yes" : "No") << std::endl;
    std::cout << "Are 8 and 9 in the same set? " << (ds.Find(8) == ds.Find(9) ? "Yes" : "No") << std::endl;
    std::cout << "Are 0 and 9 in the same set? " << (ds.Find(0) == ds.Find(9) ? "Yes" : "No") << std::endl;

    return 0;
}

```

## Simple Graph!

- 使用**邻接表**实现极简的图存储 & DFS 和 BFS的实现。
- 其实简单模拟一个队列只需要`front`和`rear`两个int变量就可以了貌似。

```cpp
#include <cstdio>
#include <cstring>
using namespace std;

const int MAXN = 100010;
const int MAXM = 200020;

struct Edge {
    int to, next, w;
    bool valid;
};

Edge edges[MAXM];
int head[MAXN];
int cnt;

void init() {
    memset(head, -1, sizeof(head));
}

void add(int from, int to, int weight) {
    edges[++cnt] = {to, head[from], weight, true};
    head[from] = cnt;
}

void remove(int from, int to) {
    for (int i = head[from]; i != -1; i = edges[i].next) {
        if (edges[i].to == to && edges[i].valid == true) {
            edges[i].valid = false;
            return;
        }
    }
}


void bfs(int start) {
    bool visited[MAXN] = {false};
    int q[MAXN];
    int front = 0, rear = 0;

    q[rear++] = start;
    visited[start] = true;

    while (front < rear) {
        int u = q[front++];
        printf("%d ", u);

        for (int i = head[u]; i != -1; i = edges[i].next) {
            if (!edges[i].valid) continue;
            int v = edges[i].to;
            if (!visited[v]) {
                visited[v] = true;
                q[rear++] = v;
            }
        }
    }
}


bool dfs_visited[MAXN];

void dfs(int u) {
    dfs_visited[u] = true;
    printf("%d ", u);

    for (int i = head[u]; i != -1; i = edges[i].next) {
        if (!edges[i].valid) continue;
        int v = edges[i].to;
        if (!dfs_visited[v]) {
            dfs(v);
        }
    }
}

int main() {
    init();

    add(1, 2, 1);
    add(2, 1, 1);
    add(2, 4, 1);
    add(4, 2, 1);
    add(3, 4, 1);
    add(4, 3, 1);

    printf("BFS from 1: \n");
    bfs(1);

    printf("\nDFS from 1: \n");
    dfs(1);
    return 0;
}
```

## LCA

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

## Floyd

```cpp
#include <climits>
#include <iostream>

#define MAX_NODES 100// Maximum number of nodes in the graph

class FloydWarshall {
private:
    int distance[MAX_NODES][MAX_NODES];
    int next[MAX_NODES][MAX_NODES];
    int numNodes;

    void initialize(int graph[][MAX_NODES]) {
        for (int i = 0; i < numNodes; i++) {
            for (int j = 0; j < numNodes; j++) {
                distance[i][j] = graph[i][j];
                if (graph[i][j] != INT_MAX && i != j) {
                    next[i][j] = j;
                } else {
                    next[i][j] = -1;
                }
            }
        }
    }

public:
    FloydWarshall(int nodes) : numNodes(nodes) {
        // Initialize with maximum distances
        for (int i = 0; i < numNodes; i++) {
            for (int j = 0; j < numNodes; j++) {
                distance[i][j] = (i == j) ? 0 : INT_MAX;
                next[i][j] = -1;
            }
        }
    }

    void addEdge(int source, int destination, int weight) {
        if (source >= 0 && source < numNodes &&
            destination >= 0 && destination < numNodes) {
            distance[source][destination] = weight;
            next[source][destination] = destination;
        }
    }

    void computeShortestPaths() {
        for (int k = 0; k < numNodes; k++) {
            for (int i = 0; i < numNodes; i++) {
                for (int j = 0; j < numNodes; j++) {
                    if (distance[i][k] != INT_MAX &&
                        distance[k][j] != INT_MAX &&
                        distance[i][j] > distance[i][k] + distance[k][j]) {
                        distance[i][j] = distance[i][k] + distance[k][j];
                        next[i][j] = next[i][k];
                    }
                }
            }
        }
    }

    void printShortestPath(int source, int destination) {
        if (source < 0 || source >= numNodes ||
            destination < 0 || destination >= numNodes) {
            std::cout << "Invalid node indices." << std::endl;
            return;
        }

        if (distance[source][destination] == INT_MAX) {
            std::cout << "No path exists from node " << source
                      << " to node " << destination << "." << std::endl;
            return;
        }

        std::cout << "Shortest path from node " << source
                  << " to node " << destination << ": ";
        std::cout << source;

        int current = source;
        while (current != destination) {
            current = next[current][destination];
            std::cout << " -> " << current;
        }

        std::cout << "\nTotal distance: " << distance[source][destination]
                  << std::endl;
    }

    void printAllPairs() {
        std::cout << "All-pairs shortest paths:\n";
        for (int i = 0; i < numNodes; i++) {
            for (int j = 0; j < numNodes; j++) {
                if (distance[i][j] == INT_MAX) {
                    std::cout << "INF\t";
                } else {
                    std::cout << distance[i][j] << "\t";
                }
            }
            std::cout << std::endl;
        }
    }
};

int main() {
    // Example graph with 4 nodes
    const int NODES = 4;
    FloydWarshall fw(NODES);

    // Add edges to the graph
    fw.addEdge(0, 1, 5);
    fw.addEdge(1, 0, 3);
    fw.addEdge(0, 3, 10);
    fw.addEdge(1, 2, 3);
    fw.addEdge(2, 3, 1);

    // Compute shortest paths
    fw.computeShortestPaths();

    // Print results
    fw.printAllPairs();
    std::cout << std::endl;

    // Print specific paths
    fw.printShortestPath(0, 3);
    fw.printShortestPath(1, 0);// No path case

    return 0;
}
```

## Dijkstra

```cpp
#include <cstdio>
#include <cstring>
using namespace std;

const int MAXN = 100010;
const int MAXM = 200020;
const int INF = 0x3f3f3f3f;

struct Edge {
    int to, next, w;
};

Edge edges[MAXM];
int head[MAXN];
int cnt;

void init() {
    memset(head, -1, sizeof(head));
    cnt = 0;
}

void add(int from, int to, int weight) {
    edges[++cnt] = {to, head[from], weight};
    head[from] = cnt;
}

// Dijkstra算法实现
void dijkstra(int start, int n, int dist[]) {
    bool visited[MAXN] = {false};
    
    // 初始化距离数组
    for (int i = 1; i <= n; i++) {
        dist[i] = INF;
    }
    dist[start] = 0;
    
    // 手动实现优先队列
    int pq[MAXN];      // 优先队列（存储节点编号）
    int pqSize = 0;     // 队列大小
    
    // 插入起始节点
    pq[pqSize++] = start;
    
    while (pqSize > 0) {
        // 找到当前距离最小的节点（模拟优先队列的pop操作）
        int minDist = INF;
        int u = -1;
        int minIndex = -1;
        
        for (int i = 0; i < pqSize; i++) {
            int v = pq[i];
            if (!visited[v] && dist[v] < minDist) {
                minDist = dist[v];
                u = v;
                minIndex = i;
            }
        }
        
        if (u == -1) break;  // 所有可达节点都已处理
        
        // 从队列中移除该节点
        pq[minIndex] = pq[--pqSize];
        
        visited[u] = true;
        
        // 遍历所有邻接边
        for (int i = head[u]; i != -1; i = edges[i].next) {
            int v = edges[i].to;
            int w = edges[i].w;
            
            if (!visited[v] && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                // 将节点加入队列
                pq[pqSize++] = v;
            }
        }
    }
}

int main() {
    init();
    
    // 构建一个简单的图
    add(1, 2, 2);
    add(1, 3, 4);
    add(2, 3, 1);
    add(2, 4, 3);
    add(3, 4, 5);
    
    int n = 4; // 节点数
    int dist[MAXN];
    
    dijkstra(1, n, dist);
    
    printf("Shortest distances from node 1:\n");
    for (int i = 1; i <= n; i++) {
        printf("to %d: %d\n", i, dist[i]);
    }
    
    return 0;
}
```

## Kosaraju

```cpp
#include <cstdio>
#include <cstring>
using namespace std;

const int MAXN = 100010;
const int MAXM = 200020;

struct Edge {
    int to, next, w;
    bool valid;
};

Edge edges[MAXM];
int head[MAXN];
int cnt;

void init() {
    memset(head, -1, sizeof(head));
    cnt = 0;
}

void add(int from, int to, int weight) {
    edges[++cnt] = {to, head[from], weight, true};
    head[from] = cnt;
}

void remove(int from, int to) {
    for (int i = head[from]; i != -1; i = edges[i].next) {
        if (edges[i].to == to && edges[i].valid == true) {
            edges[i].valid = false;
            return;
        }
    }
}

// Kosaraju's algorithm implementation
bool visited[MAXN];
int order[MAXN]; // stack for storing nodes in order of finishing times
int orderIndex;

// First DFS pass to fill the order stack
void dfsFirstPass(int u) {
    visited[u] = true;
    
    for (int i = head[u]; i != -1; i = edges[i].next) {
        if (!edges[i].valid) continue;
        int v = edges[i].to;
        if (!visited[v]) {
            dfsFirstPass(v);
        }
    }
    
    order[orderIndex++] = u; // push to stack
}

// Second DFS pass on reversed graph
void dfsSecondPass(int u, int component[], int componentId) {
    visited[u] = true;
    component[u] = componentId;
    
    for (int i = head[u]; i != -1; i = edges[i].next) {
        if (!edges[i].valid) continue;
        int v = edges[i].to;
        if (!visited[v]) {
            dfsSecondPass(v, component, componentId);
        }
    }
}

// Function to reverse the graph
void reverseGraph(int n) {
    Edge tempEdges[MAXM];
    int tempHead[MAXN];
    int tempCnt = 0;
    
    // Initialize temp head array
    memset(tempHead, -1, sizeof(tempHead));
    
    // Create reversed edges
    for (int u = 1; u <= n; u++) {
        for (int i = head[u]; i != -1; i = edges[i].next) {
            if (!edges[i].valid) continue;
            int v = edges[i].to;
            // Add reversed edge
            tempEdges[++tempCnt] = {u, tempHead[v], edges[i].w, true};
            tempHead[v] = tempCnt;
        }
    }
    
    // Copy the reversed graph back to original
    memcpy(edges, tempEdges, sizeof(tempEdges));
    memcpy(head, tempHead, sizeof(tempHead));
    cnt = tempCnt;
}

// Kosaraju's algorithm to find strongly connected components
void kosaraju(int n, int component[]) {
    // Initialize
    orderIndex = 0;
    memset(visited, false, sizeof(visited));
    
    // First pass: fill order stack
    for (int u = 1; u <= n; u++) {
        if (!visited[u]) {
            dfsFirstPass(u);
        }
    }
    
    // Reverse the graph
    reverseGraph(n);
    
    // Reset visited array
    memset(visited, false, sizeof(visited));
    
    // Second pass: process nodes in reverse order of finishing times
    int componentId = 0;
    for (int i = orderIndex - 1; i >= 0; i--) {
        int u = order[i];
        if (!visited[u]) {
            dfsSecondPass(u, component, ++componentId);
        }
    }
    
    // Restore the original graph (optional)
    reverseGraph(n);
}

int main() {
    init();

    add(1, 2, 1);
    add(2, 1, 1);
    add(2, 4, 1);
    add(4, 2, 1);
    add(3, 4, 1);
    add(4, 3, 1);

    int n = 4; // Number of nodes in the graph
    int component[MAXN]; // Stores component ID for each node
    
    kosaraju(n, component);
    
    printf("Strongly Connected Components:\n");
    for (int i = 1; i <= n; i++) {
        printf("Node %d is in component %d\n", i, component[i]);
    }
    
    return 0;
}
```

