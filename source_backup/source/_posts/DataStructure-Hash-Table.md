---
title: DataStructure Hash Table
date: 2025-04-17 13:52:32
index_img: /img/cover/hashtable.jpg
excerpt: This blog introduces common hash tables and hash functions, as well as several methods for resolving hash collisions. Finally, it discusses the usage of dynamic lookup tables in STL.
math: true
hide: false
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Hash Table
  - Finished
  - Data Structure
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Hash Table 散列表

## Introduction

我们在之前的章节中，使用**二叉查找树**来实现集合的动态查找，并通过各种方式实现了性能的优化：

- AVL树
- 红黑树
- AA树
- 伸展树

以上动态查找表都是**基于树结构**的，当然我们也可以使用线性结构来实现这一点（**Dynamic Sorted Array**），然而，如果对于大量的查询过程，我们能否对$O(\log n)$的时间复杂度做进一步优化？答案是肯定的，我们需要使用新的思路来实现**专门为集合的动态查找**。

## Definition

### 桶查找

散列表是一个**表**，将关键字值**映射**到表中的某个位置。在查找时，根据被查找的关键字值找到存储数据元素的地址，从而获得数据元素。（**桶的思想**）

最naive的**桶查找**非常容易实现，来看代码：

> 我们这里使用**随机数生成**来模拟用户的随机输入，当然你也可以手动cin，但是只有在大数据量的情况下才会体现出$O(1)$相较于$O(\log n)$的优势所在。

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-17 17:30:47
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-17 19:29:17
 * @FilePath: /Data_structure/Specific_USage/Bucket_usage.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
#include <random>
#include <cstring>

const int MAXSIZE = 100001;
int arr[MAXSIZE];

int generate_random() {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(1, 100000);
    return dis(gen);
}

int main(){
    // algorithms: given an array, for each query, reach O(1) time complexity
    int arr_length;
    int random_input = 0;
    std::cin >> arr_length;
    std::memset(arr, 0, sizeof(arr));

    for(int i = 0; i < arr_length; i++){
        random_input = generate_random();
        arr[random_input] ++;
    }

    // for the query part
    int query_num = 0;
    int query_value = 0;
    int not_found = 0;
    std::cin >> query_num;
    for(int i = 0; i < query_num; i++){
        query_value = generate_random();
        if(arr[query_value] >= 1){
            // the value is found
            std::cout << query_value << " found." << std::endl;
        }else{
            // std::cout <<  query_value << " not found" << std::endl;
            not_found ++;
        }
    }
    std::cout << "There are " << not_found << " elements that are not found." << std::endl;

    return 0;
}
```

程序输出（随机）：

```
200
10000
24185 found.
79030 found.
83479 found.
19472 found.
2067 found.
50925 found.
59530 found.
There are 9993 elements that are not found.
```

### 桶查找的问题

优势是**可以将单次查找优化到$O(1)$的时间复杂度**，但缺点是**造成了非常大的空间浪费**，而且这种方法无法处理**关键字不是整数**的情况。（例如浮点数和字符串）

因此，我们需要**尝试优化空间**，即进行**映射的压缩**，进行数学化的表述就是：

对于最原始的桶排序本质上就是通过**数组下标**实现从区间$M$（定义域）到值域$N$的映射过程，但是我们的数组容量有限，因此我们需要设计函数$f:M \to M_0,\ M_0 \subseteq M$，在将得到的$f(M_0)$映射到数组下标中。将一个较大的数字**压缩映射**到较小的，更加容易管理的数字来达到这个目的，这个函数$f$称为**散列函数（哈希函数）**。

> 这里默认$M$和$M_0$都是一段连续的区间。

我们发现散列函数的**设计**（需要计算简单）是一个非常困难的问题，同时，**因为散列函数的定义域比值域大**，所以很有可能会将两个数据元素映射到了相同的位置，会发生**哈希冲突**。（即需要保证**映射的均匀性**）。

{% note primary %}

**这何尝不是一种分块的思想呢**？

{% endnote %}

## 散列函数的设计

### 散列函数设计的关键

散列函数的设计是哈希表中的核心问题，因为这直接影响到所建立哈希表的空间复杂度。主要的困难点集中在两个方面：

- 哈希表的优势在于**常数级别的单次查询复杂度**在面对多次查询的时候非常便捷，因此散列函数的设计必须要**保证计算精简，开销小**。（比如在散列函数中做复杂的运算和取对数等昂贵的操作很明显是不合理的）
- **任何一种散列函数的设计都会遇到哈希冲突的问题**，这也是一个无法避免的问题。具体来说，就是对于下列命题：

$$\forall x ( x \in M \wedge \exists x_0(x_0 \in M_0 \wedge f(x) = x_0))$$

$$\forall x_1, x_2 \in M(f(x_1) = f(x_2) \to x_1 = x_2)$$

很显然这是无法实现的（**对于第二条**），因为$M_0$的范围比$M$要小。

> 散列函数的设计就是**牺牲一部分元素的空间**来造福整体的空间复杂度。

因此，学习一个散列函数就是看其**空间压缩的表现**和**解决哈希冲突的表现**。

### 常见的散列函数

#### 直接定址法

使用线性函数。

如果关键字集合的分布**稀疏，间隔较大且分布相对均匀**，可以使用这种方式压缩。

$$H(x) = ax + b$$

#### 除留余数法

$$H(x) = x \ \text{mod}N$$

这个方法对$N$的选择异常重要，需要根据数据的分布特征具体选择。（一般选择**素数**）

#### 平方取中法

![平方取中法](https://s1.imagehub.cc/images/2025/04/17/0711c748654bf78a67ecf1adefd5539f.png)

#### 乘法散列法

$$h(key)=⌊((key\times A)\ \text{mod} \ 1) \times m⌋$$

- 计算 $key×A$。
- 取结果的小数部分，乘以散列表的大小 $m$($M_0$的值域)。
- 取整得到最终的散列值。

- 选择一个在 $(0,1)$范围内的无理数 $A$。常用的选择是 $A = \frac{\sqrt{5}-1}{2}$或其他无理数。

#### 斐波那契散列法

本质上还是乘法散列法，只不过在选择常数$A$的时候选择了与斐波那契数列有关的**黄金分割数**。

{% note primary %}

由于斐波那契数列的性质，使用**斐波那契散列法可以在某些情况下提供更好的散列值均匀性**，从而减少碰撞的概率。

{% endnote %}

#### 数字分析法

**取数字相对分布均匀的位**作为地址的组成部分。（例如IP地址）

## 哈希冲突

在上文的分析中，我们主要介绍了**散列函数的映射过程**，证明了其可以很好的满足第一条性质。但是**哈希冲突**仍然未被讨论和解决。实际上，我们可以使用**闭散列表**和**开散列表**实现冲突问题的解决。

- 闭散列表：使用散列表的空闲单元（**借用**）
- 开散列表：再开新数组，引入额外的空间开销来补上漏洞。

> 哈希冲突本质上就是一个**赌**的过程，因为对于最坏的情况不管你再怎么设置高效的哈希函数，**需要的空间开销**还是$O(N)$级别的，我们只是希望这种情况发生的次数比较少。（就像伸展树中的90-10法则一样）

### 闭散列表

**使用散列表的空余单元**。即“鸠占鹊巢”

#### 线性探测

如果发生了冲突，不断继续往下寻找新的位置直到找到为止。

对于线性探测的**查找**：

```cpp
addr = H(key);
while(addr != null && addr != target){
	++ addr;
}

if(addr == null){
	//doesn't find!
}else{
	//find!
}
```

对于线性探测的**删除**：

我们不可以直接清空空间！因为这会影响下游元素的查找（被误判为没有被找到）

解决方案：**使用延迟删除法**，不真正的删除元素，做一个**删除标记**。

{% note primary %}

**线性探测法的二次聚集**

冲突会引起连锁反应，最终会导致冲突的加剧。这些连续被占据的单元叫做**初始聚集**，初始聚集越长，需要的插入和查找的需要访问的元素会更多。

{% endnote %}

##### 代码实现

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-17 14:22:30
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-17 20:51:35
 * @FilePath: /Data_structure/Class_implementation/close_Hash_Table.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */

#include <cstddef>
#include <iostream>
#include <queue>
#include <random>
#include <stdexcept>

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

template<class Key, class Other>
class close_Hash_Table : public dynamicSearchTable<Key, Other> {
private:
    struct Node {
        set<Key, Other> data;
        int status;
        // status: 0 for empty, 1 for active and 2 for deleted
        Node() {
            status = 0;
        }
    };

    Node *array;
    int size;

    // function pointer (hash function)
    int (*key)(const Key &x);

    // if the key value is int iteself, we no need for another function
    static int defaultKey(const int &x) {
        return x;
    }

public:
    close_Hash_Table(int length = 101, int (*f)(const Key &x) = defaultKey) {
        size = length;
        array = new Node[size];
        key = f;
    }
    ~close_Hash_Table() {
        delete[] array;
    }

    set<Key, Other> *find(const Key &x) const {
        int init_pos, pos;
        pos = init_pos = key(x) % size;
        do {
            if (array[pos].status == 0) {
                return nullptr;
            }

            if (array[pos].status == 1 && array[pos].data.key == x) {
                // find the element
                return (set<Key, Other> *) (array + pos);
            }

            pos = (pos + 1) % size;
        } while (pos != init_pos);

        return nullptr;
    }

    void insert(const set<Key, Other> &x) {
        int init_pos, pos;

        // get the position after the mapping
        init_pos = pos = key(x.key) % size;
        do {
            if (array[pos].status != 1) {
                // then we can insert the value!
                array[pos].data = x;
                array[pos].status = 1;
                return;
            } else {
                if (array[pos].data.key == x.key) {
                    // we have insert the same key value!
                    // return directly
                    return;
                }
            }
            pos = (pos + 1) % size;
        } while (pos != init_pos);
        // if there is no free space, then it will jump out of the while loop
        // you can throw somwething here
        throw std::runtime_error("The table has been occupied!");
    }

    void remove(const Key &x) {
        int init_pos, pos = 0;
        init_pos = pos = key(x) % size;
        do {
            if (array[pos].status == 0) {
                // no element will be deleted
                return;
            }
            if (array[pos].status == 1 && array[pos].data.key == x) {
                // find the specific element
                array[pos].status = 2;
                return;
            }
            pos = (pos + 1) % size;
        } while (pos != init_pos);
    }
};
int generate_random(int range = 100000) {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(1, range);
    return dis(gen);
}

std::string generate_random_string(size_t length = 20) {
    const std::string characters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(0, characters.size() - 1);

    std::string random_string;
    for (size_t i = 0; i < length; ++i) {
        random_string += characters[dis(gen)];
    }
    return random_string;
}


int main() {
    close_Hash_Table<int, std::string> s(10000);
    long long alldata_range = 1000000000;
    long long alldata_num = 8000;
    long long query_num = 1000000;
    set<int, std::string> element;
    std::queue<int> storage;

    for (int i = 0; i < alldata_num; i++) {
        set<int, std::string> element;
        element.key = generate_random(alldata_range);
        element.other = generate_random_string();
        s.insert(element);
        storage.push(element.key);
    }

    // for the searching period
    for (int i = 0; i < query_num; i++) {
        int search_key = generate_random(alldata_range);
        if (s.find(search_key) != nullptr) {
            std::cout << "Find the key value " << search_key << std::endl;
        }

        if (generate_random(1000) % 3 == 0 && !storage.empty()) {
            // randomly remove some elements
            int dele = storage.front();
            s.remove(dele);
            if (s.find(dele) == nullptr) {
                std::cout << "The key value " << dele << " has been deleted successfully." << std::endl;
            }
            storage.pop();
        }
    }

    return 0;
}

```

#### 二次探测法

**基本思路**：在线性探测法的基础之上做优化，寻找额外空间的时候避免聚集（不在连续的空间上查找）

具体实现就是：如果对于$H$的位置发生了哈希冲突，依次查询$H + i^2$的位置。

但是二次探测法会带来新的问题：

- **探测法能否保证可行性**？如何保证每一次探测均能查找到新的单元？
- 二次探测法增加了计算的复杂性。
	- 可以直接使用递推来优化（使用加法器）
- **负载因子**过多的问题。（即散列表中非空元素过多）

我们来使用数学进行证明，先回答前两个问题：

![Thm 9,2](https://s1.imagehub.cc/images/2025/04/17/cf3ee1c4d4621ca593fd8d5992d81f4b.png)

对于最后一个**负载因子**的问题，为了保证性质1，我们需要保证**数组中有一半元素是空的**，这就意味着我们需要将数组动态扩容，而此时**我们需要重新计算散列值**！（因为$M$的大小发生了变化）

#### 再次散列法

存在两个散列函数$H_1(x)$和$H_2(x)$，其中$H_1(x)$用来探测起始地址，而$H_2(x)$用来探测后续下一个探测的步长。

因此再次散列需要探测的位置为：

$$(H_1(x) + k \times H_2(x)) \ \text{mod}\  M, k = 0,1,2 \dots$$

### 开散列表

使用链表再次查找。

**插入元素**就是先通过散列函数映射到子链表所在的地址，然后再查找链表（如果有冲突的话）。

**删除元素**就是从对应的链表中删去。

#### 代码实现

```cpp
#include <cstddef>
#include <iostream>
#include <queue>
#include <random>


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

template<class Key, class Other>
class open_Hash_Table {
private:
    struct Node {
        set<Key, Other> data;
        Node *next;
        Node(const set<Key, Other> &d, Node *n = nullptr) {
            next = n;
            data = d;
        }
    };

    // like the chunking algorithms
    Node **array;
    size_t size;
    int (*key)(const Key &x);
    static int defaultKey(const int &x) {
        return x;
    }

public:
    open_Hash_Table(size_t size_, int (*f)(const Key &x) = defaultKey) {
        size = size_;
        key = f;
        array = new Node *[size];

        // initialize the array
        for (int i = 0; i < size; i++) {
            array[i] = nullptr;
        }
    }

    ~open_Hash_Table() {
        // delete all the array
        Node *p, *q;
        for (int i = 0; i < size; i++) {
            p = array[i];
            // delete the linked list of p
            while (p != nullptr) {
                q = p->next;
                delete p;
                p = p->next;
            }
        }
    }

    set<Key, Other> *find(const Key &x) const {
        int pos;
        Node *p;
        pos = key(x) % size;
        p = array[pos];
        while (p != nullptr && !(p->data.key == x)) {
            p = p->next;
        }

        if (p == nullptr) {
            return nullptr;
            // we don't find the element.
        } else {
            return (set<Key, Other> *) p;
        }
    }

    void insert(const set<Key, Other> &x) {
        int pos;
        Node *p;

        pos = key(x.key) % size;
        array[pos] = new Node(x, array[pos]);
    }

    void remove(const Key &x) {
        int pos;
        Node *p, *q;
        pos = key(x) % size;

        if (array[pos] == nullptr) {
            // no elements
            return;
        } else {
            p = array[pos];
            // search for the linked list of p

            // for the single element
            if (p->data.key == x) {
                array[pos] = p->next;
                delete p;
                return;
            } else {
                while (p->next != nullptr && !(p->next->data.key == x)) {
                    p = p->next;
                }

                if (p->next != nullptr) {
                    // we find the element
                    q = p->next;
                    p->next = q->next;
                    delete q;
                }
            }
        }
    }
};

int generate_random(int range = 100000) {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(1, range);
    return dis(gen);
}

std::string generate_random_string(size_t length = 20) {
    const std::string characters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(0, characters.size() - 1);

    std::string random_string;
    for (size_t i = 0; i < length; ++i) {
        random_string += characters[dis(gen)];
    }
    return random_string;
}


int main() {
    open_Hash_Table<int, std::string> s(10000);
    long long alldata_range = 1000000000;
    long long alldata_num = 8000;
    long long query_num = 1000000;
    set<int, std::string> element;
    std::queue<int> storage;

    for (int i = 0; i < alldata_num; i++) {
        set<int, std::string> element;
        element.key = generate_random(alldata_range);
        element.other = generate_random_string();
        s.insert(element);
        storage.push(element.key);
    }

    // for the searching period
    for (int i = 0; i < query_num; i++) {
        int search_key = generate_random(alldata_range);
        if (s.find(search_key) != nullptr) {
            std::cout << "Find the key value " << search_key << std::endl;
        }

        if (generate_random(1000) % 3 == 0 && !storage.empty()) {
            // randomly remove some elements
            int dele = storage.front();
            s.remove(dele);
            if (s.find(dele) == nullptr) {
                std::cout << "The key value " << dele << " has been deleted successfully." << std::endl;
            }
            storage.pop();
        }
    }

    return 0;
}

```

#### 时间复杂度分析

**均摊时间复杂度**：$O(1)$

**最坏时间复杂度**：$O(N)$

- 所有的元素都冲突到了一个链表中。

## STL中的动态查找表

STL中的动态查找表被称为**关联容器**，支持高效关键字的读写。

- `set`和`map`基于**红黑树**的实现，元素保持有序。
- `unordered_set`和`unordered_map`基于**哈希表**的实现，元素保持无序。

### `set`

`set` 是一个存储唯一元素的集合，元素是自动排序的。

- **唯一性**：不允许重复元素。
- **有序性**：元素会根据默认的排序规则（通常是升序）自动排序。
- **高效查找**：插入、删除和查找操作的时间复杂度为 $O(\log ⁡n)$。

### `map`

`map` 是一个存储**键值对**的关联容器，键是唯一的，每个键对应一个值。

- **唯一键**：每个键只能出现一次。
- **有序性**：键会根据默认的排序规则自动排序。
- **高效查找**：插入、删除和查找操作的时间复杂度为 $O(\log ⁡n)$。

### `unordered_set`

`unordered_set` 是一个存储唯一元素的集合，元素的顺序是无序的，基于哈希表实现。

### `unordered_map`

`unordered_map` 是一个存储键值对的关联容器，键是唯一的，基于哈希表实现，元素的顺序是无序的。

### 代码演示

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-17 14:52:33
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-17 21:29:32
 * @FilePath: /Data_structure/Specific_USage/Usage_for_DST.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
#include <map>
#include <random>
#include <set>
#include <string>
#include <unordered_map>
#include <unordered_set>

int generate_random() {
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(1, 100000);
    return dis(gen);
}

std::string generate_random_string(size_t length = 20) {
    const std::string characters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    std::random_device rd;
    std::mt19937 gen(rd());
    std::uniform_int_distribution<> dis(0, characters.size() - 1);

    std::string random_string;
    for (size_t i = 0; i < length; ++i) {
        random_string += characters[dis(gen)];
    }
    return random_string;
}

void test_set(int size = 1000) {
    std::set<int> s;
    std::set<int>::iterator p;
    int element, target;
    long long count = 0;
    std::cout << "Test for STL: set BEGIN \n\n\n";

    // insert elements in the set
    for (int i = 0; i < size; i++) {
        element = generate_random();
        s.insert(element);
        std::cout << element << " is inserted in the set." << std::endl;
    }

    // find elements in the set
    for (int i = 0; i < size * 10; i++) {
        target = generate_random();
        if (s.find(target) != s.end()) {
            // find the element
            std::cout << target << " is found in the set." << std::endl;
            count++;
        } else {
            std::cout << target << " is not found in the set." << std::endl;
        }
    }
    std::cout << count << " elements is found in the set." << std::endl;

    // delete elements in the set
    for (auto iter = s.begin(); iter != s.end();) {
        std::cout << *iter << " is in the set." << std::endl;
        iter = s.erase(iter);
        if (s.empty()) {
            std::cout << "The set is empty now!" << std::endl;
        }
    }

    std::cout << "Test for STL: set END \n\n\n";
}

void test_map(int size = 1000) {
    std::cout << "Test for STL: map BEGIN \n\n\n";
    std::map<int, std::string> s;
    int element;
    std::string element_string;
    long long count = 0;

    // insert element in s
    for (int i = 0; i < size; i++) {
        element = generate_random();
        element_string = generate_random_string();
        s.insert(std::pair<int, std::string>{element, element_string});
        std::cout << element_string << " with the value " << element << " is inserted in the map." << std::endl;
    }

    // find elements in s
    for (int i = 0; i < 10 * size; i++) {
        element = generate_random();
        auto iter = s.find(element);
        if (iter != s.end()) {
            // find the element!
            std::string target_string = iter->second;
            std::cout << target_string << " is found with the key value of " << element << std::endl;
            count++;
        } else {
            std::cout << "Key value " << element << " is not found in this map." << std::endl;
        }
    }
    std::cout << count << " elements are found in the map." << std::endl;

    // delete element in the map
    for (auto iter = s.begin(); iter != s.end();) {
        std::cout << iter->second << " with the string value " << iter->first << " is found in the map." << std::endl;
        iter = s.erase(iter);
        if (s.empty()) {
            std::cout << "The map is empty now!" << std::endl;
        }
    }

    std::cout << "Test for STL: map END \n\n\n";
}

void test_unordered_set(int size = 1000) {
    std::unordered_set<int> s;
    int element, target;
    long long count = 0;
    std::cout << "Test for STL: unordered_set BEGIN \n\n\n";

    // Insert elements into the unordered_set
    for (int i = 0; i < size; i++) {
        element = generate_random();
        s.insert(element);
        std::cout << element << " is inserted in the unordered_set." << std::endl;
    }

    // Find elements in the unordered_set
    for (int i = 0; i < size * 10; i++) {
        target = generate_random();
        if (s.find(target) != s.end()) {
            // Element found
            std::cout << target << " is found in the unordered_set." << std::endl;
            count++;
        } else {
            std::cout << target << " is not found in the unordered_set." << std::endl;
        }
    }
    std::cout << count << " elements are found in the unordered_set." << std::endl;

    // Delete elements from the unordered_set
    for (auto iter = s.begin(); iter != s.end();) {
        std::cout << *iter << " is in the unordered_set." << std::endl;
        iter = s.erase(iter);
        if (s.empty()) {
            std::cout << "The unordered_set is empty now!" << std::endl;
        }
    }

    std::cout << "Test for STL: unordered_set END \n\n\n";
}

void test_unordered_map(int size = 1000) {
    std::unordered_map<int, std::string> s;
    int element;
    std::string element_string;
    long long count = 0;
    std::cout << "Test for STL: unordered_map BEGIN \n\n\n";

    // Insert elements into the unordered_map
    for (int i = 0; i < size; i++) {
        element = generate_random();
        element_string = generate_random_string();
        s.insert({element, element_string});
        std::cout << element_string << " with the value " << element << " is inserted in the unordered_map." << std::endl;
    }

    // Find elements in the unordered_map
    for (int i = 0; i < 10 * size; i++) {
        element = generate_random();
        auto iter = s.find(element);
        if (iter != s.end()) {
            // Element found
            std::string target_string = iter->second;
            std::cout << target_string << " is found with the key value of " << element << std::endl;
            count++;
        } else {
            std::cout << "Key value " << element << " is not found in this unordered_map." << std::endl;
        }
    }
    std::cout << count << " elements are found in the unordered_map." << std::endl;

    // Delete elements from the unordered_map
    for (auto iter = s.begin(); iter != s.end();) {
        std::cout << iter->second << " with the string value " << iter->first << " is found in the unordered_map." << std::endl;
        iter = s.erase(iter);
        if (s.empty()) {
            std::cout << "The unordered_map is empty now!" << std::endl;
        }
    }

    std::cout << "Test for STL: unordered_map END \n\n\n";
}

int main() {
    // test for stl dynamic search
    test_set();
    test_map();
    test_unordered_set();
    test_unordered_map();
    return 0;
}
```

## HandMade Linked Hashmap

`linked_hashmap`相比于传统的哈希表加入了链表的部分，支持迭代器的遍历，对应STL的数据结构就是`unordered_map`。相关操作复杂度均为均摊$O(1)$级别的。

代码很长，You can see [this](https://github.com/xiyuanyang-code/Data_structure/blob/master/Class_implementation/linked_hashmap.hpp) for more details.
