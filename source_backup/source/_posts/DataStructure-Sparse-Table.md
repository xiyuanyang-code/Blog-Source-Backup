---
title: DataStructure-Sparse-Table
date: 2025-03-30 19:49:46
index_img: /img/cover/sttable.jpg
excerpt: Advanced algorithms of Sparse Table and the underlying idea of doubling. This blog will also introduce its applications.
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Tree
  - Sparse Table
  - Data Structure
  - Finished
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Sparse Table (ST)

## 倍增

倍增法，顾名思义就是「成倍增长」。我们在进行递推时，如果状态空间很大，**通常的线性递推**无法满足时间与空间复杂度的要求，那么我们可以通过成倍增长的方式，只递推状态空间中在 ![k](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7) 的整数次幂位置上的值作为代表。当需要其他位置上的值时，我们通过**「任意整数可以表示成若干个$k$的次幂项的和」这一性质，使用之前求出的代表值拼成所需的值。所以使用倍增算法也要求我们递推的问题的状态空间关于$k$的次幂具有可划分性**。通常情况下$k$取$2$。

倍增思想可以解决**RMQ**问题。

### 倍增例题

![倍增](https://s1.imagehub.cc/images/2025/04/01/e12e250bc16c69f064e435b5630b671b.png)

在m很大时，我们可以对$m$进行二进制分解，而预处理的数组就是对于$2^j$次跳跃的处理！

> **倍增的关键**就是在于将一个很长的模拟过程分割成已经预处理好的小段。

## 预处理

ST表的核心是一个二维数组 `st[i][j]`，表示从数组的第$i$个位置开始，长度为 $2^j$的区间内的最值（最小值或最大值）。

假设输入数组为 `arr`，其长度为$n$。则 `st[i][j]` 的含义如下：

$$st[i][j]=\min /\max(arr[i],arr[i+1],…,arr[i+2^j−1])$$

我们需要根据输入数组 `arr` 构建这个二维数组。

至于预处理的核心过程，**我们可以采用动态规划**的思想，**j从小到大**，因为保证是2的整数幂次，所以能够实现快速的预处理填充过程。

## RMQ Problem

给出模版题：**求区间中的最大值**

如果使用传统的二进制倍增算法，在做完预处理之后对于区间进行**二进制切分**再统计对应的分块即可。这样对于多次问询的情况可以极大提升时间复杂度。

但是这样仍然不太好，因为**二进制的严格切分**其实会变得非常松散（最坏的情况就是$O(\log n)$），如果我们只是希望求区间的最值（**可重复贡献问题**），我们可以**进行稀疏的切分**，哪怕两份切分之间存在重叠部分也没有关系，因为我们已经预处理好了，最后只是一个查表的事情。

这样，倍增切分的时间复杂度就保持在**常数级别**。

> **注意**，只有**可重复贡献问题**才可以重叠区间！如果是对于求区间和，就必须严格进行二进制的切分之后再运算。

### 代码实现

> 注意这里是 **0-based**

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-01 21:20:31
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-01 21:20:47
 * @FilePath: /20250331_fenwick/template/ST_RMQ.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
#include <cmath>
using namespace std;

const int MAXN = 1e6 + 5; // 输入数组的最大长度
const int LOG = 20;       // log2(MAXN) 的上界

int st[MAXN][LOG]; // ST表，st[i][j]表示从i开始长度为2^j的区间的最小值
int arr[MAXN];     // 输入数组
int n;             // 数组长度

// 预处理函数
void preprocess() {
    // 初始化长度为 2^0 的区间
    for (int i = 0; i < n; ++i) {
        st[i][0] = arr[i];
    }

    // 动态规划填充表格
    for (int j = 1; j < LOG; ++j) { // 区间长度从 2^1 到 2^(LOG-1)
        for (int i = 0; i + (1 << j) <= n; ++i) { // 确保区间不越界
            st[i][j] = min(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
        }
    }
}

// 查询 [L, R] 区间的最小值
int query(int L, int R) {
    int len = R - L + 1; // 查询区间的长度
    int k = log2(len);   // 找到最大的 k 满足 2^k <= len
    return min(st[L][k], st[R - (1 << k) + 1][k]);
}

int main() {
    // 输入数组长度和元素
    cin >> n;
    for (int i = 0; i < n; ++i) {
        cin >> arr[i];
    }

    // 预处理 ST 表
    preprocess();

    // 查询操作
    int q; // 查询次数
    cin >> q;
    while (q--) {
        int L, R;
        cin >> L >> R; // 查询区间 [L, R]
        cout << query(L, R) << endl; // 输出区间最小值
    }

    return 0;
}
```

## ST表例题

A 是某公司的 CEO，每个月都会有员工把公司的盈利数据送给 A，A 是个与众不同的怪人，A 不注重盈利还是亏本，而是喜欢研究「完美序列」：一段连续的序列满足序列中的数互不相同。

A 想知道区间 [L,R] 之间最长的完美序列长度。

第一行两个整数 N,M，N 表示连续 N 个月，编号为 0 到 N-1，M 表示询问的次数；

第二行 N 个整数，第 i 个数表示该公司第 i 个月的盈利值 $a_i$；

接下来 M 行每行两个整数 L,R，表示 A 询问的区间。

### 思路

记 $f(i)$ 表示以第 $i$ 个位置为结尾的最长完美序列的左端点位置。容易发现$ f(i)$ 是不降的。对于一个$ [l,r]$ 的询问，将区间中的点分成左右两部分：左边的点满足 $f(i)<L$ ，右边的点满足$ f(i)≥L$ 。对于左边的点可以直接算出答案，对于右边的点可以用 ST 表区间询问求得答案。

此处ST表储存的是对应区间每一个i在**无限延伸**的情况下满足i为右端点可以达到的最长长度。**对于这个左端点，其满足可重复贡献问题**，因此可以使用ST表解决。如果**并非无限延伸而是限制了区间**，那么无法使用动态规划的思路构建ST表，因为很明显其并不满足**结合律**，无法将小问题简单合并成大问题，无法使用ST表

### 代码实现

```cpp
#include <iostream>
#include <cmath>
#include <cstring>
using namespace std;

const int MAXN = 2e5 + 5; // 数组最大长度
const int LOG = 20;       // log2(MAXN) 的上界

int f[MAXN];              // f[i] 表示以第 i 个位置为结尾的最长完美序列的左端点位置
int st[MAXN][LOG];        // ST表，st[i][j]表示从i开始长度为2^j的区间内的最小值
int last[2][1000005];         // last[x]表示数字x最后一次出现的位置
int arr[MAXN];            // 输入数组
int n;                    // 数组长度

inline int getpos(int x){
    if(x >= 0) return 0;
    else return 1;
}
// 预处理函数：计算 f[i]
void preprocess_f() {
    memset(last, -1, sizeof(last)); // 初始化 last 数组
    for (int i = 0; i < n; ++i) {
        if (i > 0 && last[getpos(arr[i])][abs(arr[i])] >= f[i - 1]) {
            f[i] = last[getpos(arr[i])][abs(arr[i])] + 1;
        } else {
            // 如果 arr[i] 是第一次出现，f[i] = f[i-1]
            f[i] = (i > 0 ? f[i - 1] : 0);
        }
        last[getpos(arr[i])][abs(arr[i])] = i; // 更新 arr[i] 的最后一次出现位置
    }
}

// 预处理 ST 表
void preprocess_st() {
    for (int i = 0; i < n; ++i) {
        st[i][0] = i - f[i] + 1;
        //cout << st[i][0] << " ";
    }
    //cout << endl;

    for (int j = 1; j < LOG; ++j) { 
        for (int i = 0; i + (1 << j) - 1 < n; ++i) { 
            st[i][j] = max(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            //cout << st[i][j] << " ";
        }
        //cout << endl;
    }
}

//no restriction here
int query_max(int L, int R) {
    int len = R - L + 1; 
    int k = log2(len);
    //cout << "k" << k << endl;
    return max(st[L][k], st[R - (1 << k) + 1][k]);
}

//Binary search
int binary_search(int L, int R, int target) {
    int low = L, high = R;
    while (low <= high) {
        int mid = (low + high) / 2;
        if (f[mid] >= target) {
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    return low;
}

int main() {
    cin >> n;
    int m;
    cin >> m; 

    for (int i = 0; i < n; ++i) {
        cin >> arr[i];
    }

    preprocess_f();
    preprocess_st();

    

    
    while (m--) {
        int L, R;
        cin >> L >> R; 
        int p = binary_search(L, R, L);
        //cout << "p" << p << endl;

        int left_len = (p > L) ? (p - L) : 0;

        int right_len = 0;
        if (p <= R) {
            right_len = query_max(p, R);
            //cout << "rightlen" << right_len << endl;
        }

        cout << max(left_len, right_len) << '\n';
    }

    return 0;
}
```

## 再来一道例题

给定一个数组$a[i]$, 长度为$n$。(为了方便这里使用**1-based**)，给出$m$次问询，每次问询在区间$[l,r]$上是否存在$i,j(i \ne j)$，使得$a_i \otimes a_j = x$，$x$是给定好的值。($\otimes$是异或符号)

**使用ST表的模版**就是可以计算区间的**可重复贡献问题**，如何转化？

我们可以构造数组$f(i)$，定义$f(i):=\max \{ j \in [1,i) \mid a_i \otimes a_j = x \}$，因为区间的限制，我们判断**存在**只需要比较**最近的**是否在区间里面。具体来说，$f(i)$代表在下标$i$之前的满足$a_i \otimes a_j = x$的最大$j$下标，而对于区间$[l,r]$，只需要判断是否存在$i \in [l,r]$，使得$f(i) \ge l$。

> 这里并不需要考虑**另一边的r边界**，因为如果这个区间真的存在$(i,j)$，不失一般性假设$i < j$，那么对于$f(j)$是肯定能够找到i的，也就不需要重复计算了。

这样我们就把这个问题转化了对$f[i]$的区间求最大值的问题，使用ST表轻松解决。

最后一个问题，如何预处理$f[i]$？使用哈希（桶）线性扫描一遍数组就行啦。

### 代码实现

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-06 20:47:27
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-07 10:49:12
 * @FilePath: /20250406_Test3/2_2.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <cstring>
#include <iostream>
#include <cmath>

using namespace std;
const int MAX = 100001;
const int LOG = 20;
const long long HASH = (1 << 20) + 2;

int st[MAX][LOG];
long arr[MAX];
long f[MAX];
long long Hash[HASH];
int n, m, x;

// 预处理 使用f
// 对于每个位置i，f(i)表示在i之前（包括i）满足a[j] = a[i] ⊕ x的最大j值。如果不存在这样的j，则f(i) = -1
// ! all 1-based


void process_f(){
    // traverse for i
    memset(Hash, 0, sizeof(Hash));
    for(int i = 1; i <= n; i++){
        long long val = arr[i];
        long long target = val ^ x;
        // 储存target到对应的哈希数组中
        Hash[target] = i;
        // cout << "hash" << target << i << endl;

        // 得到f[i]的值（扫描完全部的之前的数）
        f[i] = Hash[arr[i]];
    }
}

// 预处理 st表
void process_st(){
    // for j = 0
    memset(st, 0, sizeof(st));
    for(int i = 1; i <= n; i++){
        st[i][0] = f[i];
    }

    for (int j = 1; j < LOG; ++j) { // 区间长度从 2^1 到 2^(LOG-1)
        for (int i = 1; i + (1 << j) <= n + 1; ++i) { // 确保区间不越界
            st[i][j] = max(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
        }
    }
}

//no restriction here
bool query_max(int L, int R) {
    int len = R - L + 1; 
    int k = log2(len);
    //cout << "k" << k << endl;
    return (max(st[L][k], st[R - (1 << k) + 1][k]) >= L);
}

int main(){
    cin >> n >> m >> x;
    for(int i = 1; i <= n; i++){
        cin >> arr[i];
    }

    process_f();
    process_st();
    
    int l,r;
    while(m--){
        // query
        cin >> l >> r;
        cout << (query_max(l,r) ? "Yes": "No")<< '\n';

    }
    return 0;
}
```
