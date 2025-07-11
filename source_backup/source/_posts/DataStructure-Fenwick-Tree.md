---
title: DataStructure-Fenwick-Tree
date: 2025-03-30 19:49:40
index_img: /img/cover/fenwick.jpg
excerpt: Advanced algorithms of Fenwick Tree, including its applications.
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Tree
  - Fenwick Tree
  - Data Structure
  - Finished
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
# Fenwick Tree (树状数组)

## Introduction

**树状数组**的核心在于高效处理前缀和，并且使用树状结构。

> 为了方便计算，**这里的数组全部使用1-based**.

![树状数组](https://oi-wiki.org/ds/images/fenwick.svg)

如上图所示，$a_i$是原数组的值，而$c_i$是根据$a_i$所生成的树状数组

## Template Operations

### 预处理（区间管辖）

上面的图非常清晰，每一个$c_i$都管辖了对应区间的前缀和，那如何使用代码表示？

给出函数`lowbit(x)`，返回在二进制下**最低位1和后面的0组成的数**。

```cpp
int lowbit(int x) {
  return x & -x;
}
```

不难发现，$c_i$管辖的区间就是$[x-lowbit(x)+1,x]$.

具体而言，`lowbit()`函数就是把这个值减去了**小于他的最小的二进制组成的幂次**。这一个部分在后文详细解释。

### 区间求和查询

本质上就是二进制的拆解过程。

例如$7=1+2+4$，因此

$$ {\textstyle \sum_{i=1}^{7}a_i}= {\textstyle \sum_{i=1}^{4}a_i} + {\textstyle \sum_{i=5}^{6}a_i} + {\textstyle \sum_{i=7}^{7}a_i}$$

$${\textstyle \sum_{i=1}^{7}a_i} = c_4 + c_6 + c_7$$

一般的，${\textstyle \sum_{i=a}^{b}a_i} = {\textstyle \sum_{i=1}^{b}a_i} - {\textstyle \sum_{i=1}^{a}a_i}$

那如何使用`lowbit()`实现这个**前缀切分的过程**？以7为例，考虑下面的程序

```cpp
#include <iostream>
using namespace std;

inline long long lowbit(long long x){
    return x & (-x);
}

int main(){
    cout << "Hello world" << endl;
    int x = 7;
    while(x > 0){
        cout << lowbit(x) << endl;
        x -= lowbit(x);
    }
    return 0;
}
```

```
Hello world
1
2
4
```

换句话说，$lowbit(7)=1$ ,这个1就代表$a_7$这一个元素，储存在$c_7$中。

接下来更新$x$的值，此时7变成了6，$lowbit(6)=2$，代表$c_6$中储存了**2个元素（$a_5,a_6$）**。

这样，我们就找到了区间查询的做法：

```cpp
/*
!区间查询
*/
long long getsum(long long x){
    long long ans = 0;
    while(x > 0){
        ans += c[x];
        x -= lowbit(x);
    }
    return ans;
}

```

### 单点修改

#### Propositions

（这一段截自OI wiki，讲的太好了）

- $x \le y$，则$c[x]$和$c[y]$不交或存在包含关系。
- $c[x]$真包含于$c[x+lowbit(x)]$
- 对于$x$和$x + lowbit(x)$之间的元素，$c[x]$和$c[y]$严格不交

![千言万语不如再看看这张图](https://oi-wiki.org/ds/images/fenwick.svg)

- $height_x=\log_{2}lowbit(x)$
- $lowbit(x) < lowbit(fa[x])$

**基本思路**：以$a_i$为起点，沿着树不断往上一直走到头（所有包含这个元素的前缀和都需要修改）

```cpp
void add(int x, int k) {
  while (x <= n) {  
    c[x] = c[x] + k;
    x = x + lowbit(x);
  }
}
```

> 注意，在考虑树状数组的树形态时，我们不考虑树状数组大小的影响，即我们认为这是一棵无限大的树，方便分析。实际实现时，我们只需保证$x \le n$，其中$n$是原数组长度。

因此，我们给出完整的代码：

```cpp
struct fenwick_1
//
{
    int N;
    long long C[MAXN];

    void init(int n){
        N = n;
        memset(C, 0, sizeof(C));
    }

    void addSingle(int pos, long long val) {
        for(int i = pos; i <= N; i += lowbit(i)){
            C[i] += val;
        }
    }

    long long rangeAsk(int pos) {
        long long ans = 0;
        for(int i = pos; i > 0; i -= lowbit(i)){
            ans += C[i];
        }
        return ans;
    }

    long long rangeAsk2(int l, int r) {
        return rangeAsk(r) - rangeAsk(l - 1);
    }
};

```

### 建树

在建树操作中，可以首先全部初始化为0，然后单点插入$n$个元素，时间复杂度$O(n\log n)$。

对于上述两种基本操作，均可以达到$O(\log n)$的时间复杂度。

## 区间修改和单点查询

> 以下内容节选自**OI Wiki**

考虑序列 $a$ 的差分数组 $d$，其中 $d[i] = a[i] - a[i-1]$。由于差分数组的前缀和就是原数组，所以

$$
a_i = \sum_{j=1}^{i} d_j.
$$

一样地，我们考虑将查询区间和通过差分转化为查询前缀和。那么考虑查询 $a[1 \dots r]$ 的和，即 $\sum_{i=1}^{r} a_i$，进行推导：

$$
\sum_{i=1}^{r} a_i = \sum_{i=1}^{r} \sum_{j=1}^{i} d_j
$$

观察这个式子，不难发现每个 $d_j$ 总共被加了 $r-j+1$ 次。接着推导：

$$
\sum_{i=1}^{r} \sum_{j=1}^{i} d_j = \sum_{i=1}^{r} d_i \times (r-i+1)= \sum_{i=1}^{r} d_i \times (r+1) - \sum_{i=1}^{r} d_i \times i
$$

$\sum_{i=1}^{r} d_i$ 并不能推出 $\sum_{i=1}^{r} d_i \times i$ 的值，所以要用**两个树状数组分别维护 $d_i$ 和 $d_i \times i$ 的和信息**。

**使用差分数组**的优势在于**对于区间内部的点，同增不会改变差分的值**，因此，区间修改可以转化为**对差分数组的单点修改**，之后使用模版即可。

因此，我们在额外维护两个数组的情况下，可以实现如下操作：

已知模版操作：**区间求和查询** & **单点修改**

- 区间修改：**对差分数组的单点修改**
- 区间求和：**对两个差分数组（$d_i * i$）**的单点查询
- 单点操作就是**区间操作的退化情况**

> 截至，我们就可以完成四种**一维数组**的基本操作了！值得一题的是，在这里我们并不需要维护**对原数组的树状数组**，我们应该维护**两个差分数组的树状数组**，并转化为相对应的模版操作。

给出代码实现：

```cpp
struct fenwick_2
{
    int N;
    long long C1[MAXN];
    long long C2[MAXN];

    void init(int n){
        N = n;
        memset(C1, 0, sizeof(C1));
        memset(C2, 0, sizeof(C2));
    }

    void addRange1(int pos, long long val){
        //对于差分数组的单点修改操作
        for(int i = pos; i <= N; i += lowbit(i)){
            C1[i] += val;
            C2[i] += val * pos;
        }
    }

    void addRange2(int posl, int posr, long long val){
        //对差分数组使用两次单点修改操作，就可以得到区间修改
        addRange1(posl, val);
        addRange1(posr + 1, -val);
    }

    long long askRange1(int pos){
        //从1到pos的区间求和
        long long ans = 0;
        for(int i = pos; i > 0; i -= lowbit(i)) {
            ans += (pos + 1)*C1[i] - C2[i];
        }
        return ans;
    }

    long long askRange2(int l, int r){
        return askRange1(r) - askRange1(l - 1);
    }

    //退化为单点的修改和查询
    void Addsingle(int pos, long long val){
        addRange2(pos, pos, val);
    }

    long long Asksingle(int pos){
        return askRange2(pos, pos);
    }

};
```

## 二维树状数组

### 问题重述

给定一个二维数组$A_{m \times n}$，要求实现如下操作：

- 单点修改$A[x][y]$
- 单点查询$A[x][y]$
- 区间和查询，查询左上角为$(x_1,y_1)$右下角为$(x_2,y_2)$的矩阵和
- 区间修改，左上角为$(x_1,y_1)$右下角为$(x_2,y_2)$的矩阵统一加上$x$

### 建立新的树状数组

在一维树状数组中，数组$C[x]$记录了的是右端点为$x$、长度为$lowbit(x)$的区间的区间和。

那么我们也可以类似地定义$C[x][y]$记录的是右下角为$(x,y)$，高为 $lowbit(x)$，宽为 $lowbit(y)$ 的区间的区间和。

这样的过渡非常自然，我们因此也可以类似完成上面的4个基本操作。

### 单点修改 & 区间查询（模版操作）

**单点修改**的基本思路保持不变：沿着最开始的叶节点不断向上修改祖先节点的值，最终到达最大值。

在二维数组中，修改一个单点，那么对于横轴和纵轴，相当于套两层循环，并且不断的迭代，代码同理：

```cpp
void add(int x, int y, int v) {
  for (int i = x; i <= n; i += lowbit(i)) {
    for (int j = y; j <= m; j += lowbit(j)) {
      c[i][j] += v;
    }
  }
}
```

**对于子矩阵的查询**，先给出查询**左上角起点为$(1,1)$的矩阵**的和的公式：

```cpp
//ask for the submatrix (1,1) -> (x,y)
    long long ask(int x, int y){
        long long ans = 0;
        for(int i = x; i > 0; i -= lowbit(i)){
            for(int j = y; j > 0; j -= lowbit(j)){
                ans += C[i][j];
            }
        }

        return ans;
    }
```

在二维数组中，${\textstyle \sum_{i=a}^{b}a_i} = {\textstyle \sum_{i=1}^{b}a_i} - {\textstyle \sum_{i=1}^{a}a_i}$ 需要被推广，也就是

![Sum](https://s1.imagehub.cc/images/2025/04/01/07522d131e834adfb89b55cac7d02e71.png)

这样，最基本的模版操作就可以推广了：

```cpp
#include <iostream>
#include <cstring>

const int MAXM = 100000;
const int MAXN = 100000;

inline int lowbit(int x){
    return x & (-x);
}

/**
 * @brief only for two operations: modify a single point and sum a submatrix
 * 
 */
struct fenwick_2d_1{
    int m,n;//size
    long long C[MAXM][MAXN];//fenwick tree

    fenwick_2d_1(int m_, int n_){
        m = m_;
        n = n_;
        memset(C, 0, sizeof(C));
    }
    
    /**
     * @brief Modify a single element
     * 
     * @param x x-index (1-based)
     * @param y y-index (1-based)
     * @param val plus val
     */
    void add(int x, int y, long long val){
        for(int i = x; i <= m; i += lowbit(i)){
            for(int j = y; j < n; j += lowbit(j)){
                C[i][j] += val;
            }
        }
    }

    //ask for the submatrix (1,1) -> (x,y)
    long long ask(int x, int y){
        long long ans = 0;
        for(int i = x; i > 0; i -= lowbit(i)){
            for(int j = y; j > 0; j -= lowbit(j)){
                ans += C[i][j];
            }
        }

        return ans;
    }

    //(p,q) -> (x,y)
    long long ask_sub(int p, int q, int x, int y){
        return ask(x, y)-ask(p - 1, y)-ask(q - 1, y)+ask(p, q);
    }
};
```

### 使用差分数组完成四个基本操作

本质上还是**一维变二维**的过程。

首先给出二维条件下差分数组的定义：

$$d[i][j]=a[i][j]−a[i−1][j]-a[i][j−1]+a[i−1][j−1]$$

> 此处需要保证**前缀和**和**差分**是一对逆运算。

#### 区间修改和单点查询

对于区间修改，同理，**对于差分数组来说只有四个顶角的点会发生变动**（），证明略。

对于单点查询，可以转化为**对差分数组**的区间查询。

```cpp
struct fenwick_2d_2
{
    int m, n;
    long long C[MAXM][MAXN];//此处维护差分数组
    void init(int m_, int n_){
        m = m_;
        n = n_;
        memset(C, 0, sizeof(C));
    }

    /**
     * @brief submatrix(1,1) -> (x,y) all add val
     * 
     * @param x 
     * @param y 
     * @param val 
     * 这里的代码和上一个代码的add完全相同，但是意义不一样，因为这里的C维护的是差分数组
     */
    void add(int x, int y, long long val){
        for(int i = x; i <= m; i += lowbit(i)){
            for(int j = y; j <= n; j += lowbit(y)){
                C[i][j] += val;
            }
        }
    }

    /**
     * @brief submatrix(x1, y1) -> (x2, y2) all adds x
     * 
     * @param x1 
     * @param y1 
     * @param x2 
     * @param y2 
     * @param x 
     */
    void range_add(int x1,int y1,int x2,int y2,long long x) {
        add(x1,y1,x);
        add(x1,y2+1,-x);
        add(x2+1,y1,-x);
        add(x2+1,y2+1,x);
    }

    /**
     * @brief return the value of index (x,y)
     * 
     * @param x 
     * @param y 
     * @return long long 
     */
    long long ask(int x,int y)
    {
        long long ret=0;
        for(int i = x; i > 0; i -= lowbit(i)){
            for(int j = y;j > 0;j -= lowbit(j)){
                ret += C[i][j];
            }
        }
            
        return ret;
    }
};
```

#### 更加复杂的一般化？

如果我需要**同时支持四种操作**？此时会更加复杂，贴上来自**OI wiki**的补充证明：

{% note primary %}

对于点 $(x, y)$，它的二维前缀和可以表示为：

$$
\sum_{i=1}^{x} \sum_{j=1}^{y} \sum_{h=1}^{i} \sum_{k=1}^{j} d(h, k)
$$

原因就是差分的前缀和的前缀和就是原本的前缀和。

和一维树状数组的「区间加区间和」问题类似，统计 $d(h, k)$ 的出现次数，为 $(x - h + 1) \times (y - k + 1)$。

然后接着推导：

$$
 \sum_{i=1}^{x} \sum_{j=1}^{y} \sum_{h=1}^{i} \sum_{k=1}^{j} d(h, k) = \sum_{i=1}^{x} \sum_{j=1}^{y} d(i, j) \times (x - i + 1) \times (y - j + 1) 
$$
$$=  \sum_{i=1}^{x} \sum_{j=1}^{y} d(i, j) \times (xy + x + y + 1) - d(i, j) \times i \times (y + 1) - d(i, j) \times j \times (x + 1) + d(i, j) \times i \times j$$

{% endnote %}

**因此，在二维数组中，需要额外维护4个差分数组！**

- 区间修改：对差分数组做操作
- 区间查询：对差分数组做操作
- 单点查询：**退化**，可以转化为对一个1*1的子矩阵进行查询
- 单点修改：**退化**，可以转化为对一个1*1的子矩阵进行修改

```cpp
struct fenwick_2d_3
{
    int m, n;
    long long C1[MAXM][MAXN];
    long long C2[MAXM][MAXN];
    long long C3[MAXM][MAXN];
    long long C4[MAXM][MAXN];

    void init(int m,int n)
    {
        this->n = n;
        this->m = m;
        memset(C1,0,sizeof(C1));
        memset(C2,0,sizeof(C2));
        memset(C3,0,sizeof(C3));
        memset(C4,0,sizeof(C4));
    }

    //区间修改
    /**
     * @brief Add val for single element (x,y)
     * 
     * @param x 
     * @param y 
     * @param val 
     */
    void add(int x, int y, long long val) {
        for(int i = x;i <= m;i += lowbit(i))
        {
            for(int j = y;j <= n;j += lowbit(j))
            {
                C1[i][j] += val;
                C2[i][j] += val * x;
                C3[i][j] += val * y;
                C4[i][j] += val * x * y;
            }
        }
    }

    /**
     * @brief Add val for all elements in the submatrix 
     * 
     * @param x 
     * @param y 
     * @param val 
     */
    void add_submatrix(int x1, int y1, int x2, int y2, long long val) {
        add(x1,y1,val);
        add(x1,y2+1,-val);
        add(x2+1,y1,-val);
        add(x2+1,y2+1,val);
    }

    //区间查询
    /**
     * @brief the sum of the submatrix (1,1) -> (x,y)
     * 
     * @param x 
     * @param y 
     * @return long long 
     */
    long long rangeAsk1(int x, int y){
        long long ans = 0;
        for(int i = x; i > 0; i -= lowbit(i)){
            for(int j = 0; j > 0; j -= lowbit(j)){
                ans += (x + 1)*(y + 1)*C1[i][j];
                ans -= (y + 1)*C2[i][j] + (x + 1)*C3[i][j];
                ans += C4[i][j];
            }
        }
        return ans;
    }

    long long rangeAsk2(int x1, int y1, int x2, int y2){
        return rangeAsk1(x2,y2) - rangeAsk1(x1-1,y2) - rangeAsk1(x2,y1-1) + rangeAsk1(x1-1,y1-1);
    }

    //单点查询
    long long ask(int x,int y)
    {
        return rangeAsk2(x, y, x, y);
    }

    //单点修改的退化情况
    void modifySingle(int x, int y, long long val){
        add_submatrix(x, y, x, y, val);
    }
};


```

至此，我们已经实现了**四种最基本的模版操作！**

## 权值树状数组

> 一个序列$a[x]$的权值数组$b[x]$，满足$b[x]$的值为$x$在$a[\ ]$中的出现次数。在权值数组中，**值与值之间的相对关系**比**值的绝对大小**要更加重要。因此，如果对于值域过大的情况，可以先使用**离散化**操作后再建立等效的权值数组。另外，权值数组是原数组无序性的一种表示：它重点描述数组的元素内容，忽略了数组的顺序，若两数组只是顺序不同，所含内容一致，则它们的权值数组相同。

由于权值数组考虑**值与值之间的大小关系**，因此将重点放在**查询排名**而并非区间和上面。

