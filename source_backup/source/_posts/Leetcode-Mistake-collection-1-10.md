---
title: Leetcode-Mistake-collection-1-10
date: 2024-12-10 8:05:02
index_img: /img/cover/Leetcode.jpg
excerpt: Leetcode-Mistake-Collection-Episode1, focusing on arrays and Hashmap.
categories:
  - Algorithm
  - LeetCode Mistake Collection
tags:
  - Leetcode notes
  - Finished
  - algorithm
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Leetcode-Mistake-collection-1-10

# 程设错题总结 1~5

【完成时间：2024-10-15】

【补档时间：2024-12-9】

## 1.Leetcode202 Happynumber

[原题链接](https://leetcode.cn/problems/happy-number/description/)

---

编写一个算法来判断一个数 `n` 是不是快乐数。

**「快乐数」** 定义为：

- 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
- 然后重复这个过程直到这个数变为 1，也可能是 **无限循环** 但始终变不到 1。
- 如果这个过程 **结果为** 1，那么这个数就是快乐数。

如果 `n` 是 *快乐数* 就返回 `true` ；不是，则返回 `false` 。

 **示例 1：**

```
输入：n = 19
输出：true
解释：
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

---

### 1.使用快慢指针解决循环问题

当快指针追上慢指针时，代表完成了一次循环。

```C++
class Solution {
public:
    int happynumber(int n){
        int sum=0;
        while(n!=0){
            sum+=(n%10)*(n%10);
            n/=10;
        }
        return sum;
    }
    bool isHappy(int n) {
        int i=n,j=n;
        do{
            i=happynumber(i);
            j=happynumber(j);
            j=happynumber(j);
            if(i==1){
                return 1;
            }
        }
        while(i!=j);
        return 0;
    }
};
```

### 2.使用哈希表存储

此处使用容器：unordered_set

```c++
class Solution {
public:
    int happynumber(int n){
        int sum=0;
        while(n!=0){
            sum+=(n%10)*(n%10);
            n/=10;
        }
        return sum;
    }
    bool isHappy(int n) {
        unordered_set <int> list;
        while(n!=1){
            if(list.find(n)!=list.end()){
                return false;
            }else{
                list.insert(n);
            }
            n=happynumber(n);
        }
        return true;
    }
};
```

## 2.Acwing 817 数组去重

[原题链接](https://www.acwing.com/problem/content/819/)

---

给定一个长度为 nn 的数组 aa，请你编写一个函数：

```
int get_unique_count(int a[], int n);  // 返回数组前n个数中的不同数的个数
```

**输入格式**

第一行包含一个整数 nn。

第二行包含 nn 个整数，表示数组 aa。

**输出格式**

共一行，包含一个整数表示数组中不同数的个数。

**数据范围**

1≤n≤10001≤n≤1000,
1≤ai≤10001≤ai≤1000。

**输入样例：**

```
5
1 1 2 4 5
```

**输出样例：**

```
4
```

---

### method1：使用STL

**unique函数**：

unique是C++语言中的STL函数，包含于<algorithm>头文件中。 **功能是将数组中相邻的重复元素去除**。 然而其本质是将重复的元素移动到数组的末尾，最后再将迭代器指向第一个重复元素的下标。

```c++
#include <iostream>
#include <algorithm>

using namespace std;

int main()
{
    int n, s[1010];
    cin >> n;
    for (int i = 0; i < n; i ++) cin >> s[i];
    sort(s, s + n);
    cout << unique(s, s + n) - s;

    return 0;
}
```

### method2：基本方法

```c++
#include <iostream>

using namespace std;

int b[1001];
int sum=0;
/*两个数组的区别：
a[]代表输入的数组（待去重的数组）
b[]数组是一个状态数组，其下标对应的值和a[]对应，初始值均为0，一旦出现a[i],即代表下标为a[i]的b[]被访问过，状态值变为1。（且只有初次访问是生效的）
*/
int get_unique_count(int a[], int n)
{
    for(int i=1;i<=n;i++)
    {
        if(b[a[i]]==0)
        b[a[i]]=1;
        sum++;
    }
    return sum;
}
//函数作用：统计数组中一共出现了多少不相同的数。
int main()
{
  int n;
  cin>>n;
  int a[n+1];
  for(int i=1;i<=n;i++)
  {
      cin>>a[i];
  }
  cout<<get_unique_count(a,n);
  return 0;
}

```

## 3.ACwing 15 二维数组的查找

[题目链接](https://www.acwing.com/problem/content/16/)

在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。

请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**数据范围**

二维数组中元素个数范围 [0,1000][0,1000]

**样例**

```
输入数组：

[
  [1,2,8,9]，
  [2,4,9,12]，
  [4,7,10,13]，
  [6,8,11,15]
]

如果输入查找数值为7，则返回true，

如果输入查找数值为5，则返回false。
```

![Scanning](3.png)

思路：

​	1.暴力循环（两个for循环嵌套）

​	2.通过数组大小关系的规律实现**逐步逼近**的策略

```c++
bool Find(int target, vector<vector<int> > array) {
        int row=array.size();
        int col=array[0].size();
        int i=row-1;
        int j=0;
    //定义扫描的初始位置，(row-1,0),即数组棋盘的左下角
        while(i>=0&&j<col)
        {
            if(target==array[i][j])
                return true;
            //得到return后即可跳出循环，代表查找成功
            else if(target<array[i][j])
                i--;
            //删去最下面一行(判断大小关系)
            else
                j++;
            //删去第一列，移动到第二列(判断大小关系)
        }
        return false;
    }

```

## 4.Acwing3801 最佳连续子数组

[原题链接](https://www.acwing.com/problem/content/3804/)

给定一个长度为 nn 的数组 a1,a2,…,ana1,a2,…,an。

请你找到其中的最佳**连续**子数组。

最佳连续子数组需满足：

1. 子数组内各元素的算术平均数（即所有元素之和除以元素个数）尽可能大。
2. 满足条件 11 的前提下，子数组的长度尽可能长。

输出最佳连续子数组的长度。

**输入格式**

第一行包含整数 TT，表示共有 TT 组测试数据。

每组数据，第一行包含整数 nn。

第二行包含 nn 个整数 a1,a2,…,ana1,a2,…,an。

**输出格式**

每组数据输出一行结果，表示最佳连续子数组的长度。

### 最暴力解法：分别枚举首项和尾项 O(n^2)

```c++
#include <iostream>
#include <vector>
using namespace std;
int testbestarray(){
    int n; cin>>n;
    int numarray[n];
    int sum=0,targetnumber=0;
    double maxave=0,ave;
    for(int i=0;i<n;i++){
        cin>>numarray[i];
    }
    for(int i=0;i<n;i++){
        for(int j=i;j<n;j++){
            for(int k=i;k<=j;k++){
                sum+=numarray[k];
            }
            //在已知i，j的情况下，遍历对子数组求和
            ave=double(sum)/(j-i+1);
            if(ave>maxave){
                maxave=ave;
                targetnumber=(j-i+1);
            }
            else if(ave==maxave){
                if((j-i+1)>targetnumber){
                    targetnumber=(j-i+1);
                }
            }
            sum=0;
        }
    }
    return targetnumber;
}
int main (){
    int T; cin>>T;
    int count=0;
    while (count<T){
        cout<<testbestarray()<<endl;
        count++;
    }
    return 0;
}
```

### 解法优化：

**分治的思想**：将一个大问题拆分成若干个小问题再分别解决。

子问题1：最大子数列的值

​	***最大子数列的值一定等于数列中最大项的值***

子问题2：在子数列平均值最大的情况下，找到最长长度：

​	***找到数列中是否有若干项连续，且值均为最大值。***

优化代码：

```c++
#include <iostream>
using namespace std;
int testbestarray(){
    int n; cin>>n;
    int numarray[n];
    int sum=0,max=0;
    int count=0;int countmax=0;
    for(int i=0;i<n;i++){
        cin>>numarray[i];
        max=(numarray[i]>max?numarray[i]:max);
    }
    for(int i=0;i<n;){
        if(i<n&&numarray[i]==max){
            while(i<n&&numarray[i]==max){
            count++;
            i++;
            }
            i++;
            countmax=(countmax<count?count:countmax);
            count=0;
        }
        else if(i<n){
            i++;
        }
    }
    //这一段for循环是筛查的核心，如果某元素是列表中最大值，则进入while循环直到第一个非最大值元素的出现终止while循环，同时结束count++，并最大化countmax。在遍历完一整个数组后，即可得到最大子区间的长度。
    return countmax;
}
int main (){
    int T; cin>>T;
    int count=0;
    while (count<T){
        cout<<testbestarray()<<endl;
        count++;
    }
    return 0;
}


```

### 进一步简化：

```C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

const int N = 10e5 + 10;
int a[N];

int main()
{
    int T;
    cin >> T;
    while(T--)
    {
        int n;
        cin >> n;

        int m = 0;
        for(int i = 0; i < n; i++)
        {
            cin >> a[i];
            m = max(m, a[i]);//m保存最大值
        }

        int res = 1;
        for(int i = 0; i < n;i++)//求长度
        {
            int t = 0;
            while(i < n && a[i] == m)
            {
                t++;
                i++;
            }
            res = max(res, t);
        }
        /*核心while循环：
        此处不用再添加if语句，直接使用外部的for循环即可。
        */
        cout << res << endl;
    }
    return 0;
}

```

## 5.ACwing 862 三元组排序

[原题链接](https://www.acwing.com/problem/content/864/)

给定 N 个三元组(x,y,z)，其中 xx 是整数，yy 是浮点数，zz 是字符串。

请你按照 x 从小到大的顺序将这些三元组打印出来。

数据保证不同三元组的 xx 值互不相同。

**输入格式**

第一行包含整数 N。

接下来 N 行，每行包含一个整数 x，一个浮点数 y，一个字符串 z，表示一个三元组，三者之间用空格隔开。

**输出格式**

共 N 行，按照 x 从小到大的顺序，每行输出一个三元组。

注意，所有输入和输出的浮点数 y 均保留两位小数。

### 思路：

- 最基本的排序使用排序算法（此处用algorithm库中的sort（）函数）
- 如何构建一一对应的关系？
  - map容器
  - pair：将两组数据整合成一个数据对
    - （int,（double,string））
    - 对pair类型的int数排序，一一对应的（double，string）满足映射

### 代码实现：

#### ①最基本的数组实现

排序算法（Bubblesort）+交换函数swap（）（内置在C++库中）

```c++
#include <iostream>
#include <string>
using namespace std;
int main (){
    int n; cin>>n;
    int intlist[n];
    double doublelist[n];
    string stringlist[n];
    //定义三个数组，分别储存三种不同的数据结构。
    for(int i=0;i<n;i++){
        cin>>intlist[i]>>doublelist[i]>>stringlist[i];
    }
    
    for(int j=n;j>1;j--){
        for(int i=0;i<j-1;i++){
            if(intlist[i]>intlist[i+1]){
                swap(intlist[i],intlist[i+1]);
                swap(doublelist[i],doublelist[i+1]);
                swap(stringlist[i],stringlist[i+1]);
            }
        }
    }
    //核心算法：冒泡排序
    //从第一位开始逐项与后一位冒泡比较确定是否交换，第一轮下来就确定末尾项为最大值。
    //之后通过外层的for循环逐步确定直至首项
    for(int i=0;i<n;i++){
        printf("%d %.2lf",intlist[i],doublelist[i]);
        cout<<" "<<stringlist[i]<<endl;
    }
    return 0;
}

```

#### ②map映射

```C++
#include<iostream>
#include<cstring>
#include<cstdio>
#include<map>

#define x first
#define y second

using namespace std;

const int N = 10010;
typedef pair<double, string> PII;
map<int, PII> ans;
//定义了一个从int向PII的映射，就不用使用两次pair了
int n;

int main()
{
    int a;
    double b;
    string c;
    cin >> n;

    for (int i = 0; i < n; i ++ )
    {
        cin >> a >> b >> c;
        ans.insert({a, {b, c}});
    }

    
    for (auto iter = ans.begin(); iter != ans.end(); iter ++ )
        printf("%d %.2f %s\n", iter->x, iter->y.x, iter->y.y.c_str());  
    /*这里 iter是一个迭代器
    iter->first代表指向map类型的first成员函数（即自变量）
    iter->second代表指向因变量
    iter->second.first代表指向因变量pair的第一个元素
    iter->second.second               第二个元素
    c_str()返回一个指向字符串的指针
    */
    return 0;
}
```

#### ③使用pair类型

```c++
#include<iostream>
#include<cstring>
#include<algorithm>
#include<vector>

#define x first
#define y second

using namespace std;
const int N = 10010;
typedef pair<int, pair<double, string >> PII;

vector<PII> ans;
int n, a;
double b;
string s;
int main()
{
    cin >> n;
    for (int i = 0; i < n; i ++ )
    {
        cin >> a >> b >> s;
        ans.push_back({a, {b, s}});
        //vector类型的push_back函数，{a,{b,s}}是一个PII类型的数据
    }
    sort(ans.begin(), ans.end());
    //使用sort()函数实现ans这个vector的自动排序(默认升序)
    for (auto i: ans)
        printf("%d %.2lf %s\n",i.x, i.y.x, i.y.y.c_str());   
    //若i是一个pair类型，则i.first代表pair的第一个元素，而i.second代表pair的第二个元素。
    return 0;
}
```





# 程设错题 6~10 20241015~20241020

## 1.经典问题：找质数

### 方法1：基本枚举法 O(n^3/2)

```C++
class Solution {
public:
    bool isprime(int n){
        if(n==2||n==3){
            return true;
        }
        for(int i=2;i*i<=n;i++){
            if(n%i==0){
                return false;
            }
        }
        return true;
    }
    int countPrimes(int n) {
        int sum=0;
        for(int i=2;i<n;i++){
            sum+=isprime(i);
        }
        return sum;
    }
};
```

### 方法2：埃氏筛

​	枚举没有考虑到数与数的关联性，因此难以再继续优化时间复杂度。接下来我们介绍一个常见的算法，该算法由希腊数学家厄拉多塞（Eratosthenes）提出，称为厄拉多塞筛法，简称埃氏筛。

​	我们考虑这样一个事实：**如果 x 是质数，那么大于 x 的 x 的倍数 2x,3x,… 一定不是质数**，因此我们可以从这里入手。

​	我们设 isPrime[i] 表示数 i 是不是质数，如果是质数则为 1，否则为 0。从小到大遍历每个数，如果这个数为质数，则将其所有的倍数都标记为合数（除了该质数本身），即 0，这样在运行结束的时候我们即能知道质数的个数。

​	这种方法的正确性是比较显然的：这种方法显然不会将质数标记成合数；另一方面，当从小到大遍历到数 x 时，倘若它是合数，则它一定是某个小于 x 的质数 y 的整数倍，故根据此方法的步骤，我们在遍历到 y 时，就一定会在此时将 x 标记为 isPrime[x]=0。因此，这种方法也不会将合数标记为质数。

​	当然这里还可以继续优化，对于一个质数 x，如果按上文说的我们从 2x 开始标记其实是冗余的，应该直接从 x⋅x 开始标记，因为 2x,3x,… 这些数一定在 x 之前就被其他数的倍数标记过了，例如 2 的所有倍数，3 的所有倍数等。

**这种方法不用再判断单个数是否为质数，可以减少筛查从1~n的次数**

**埃氏筛的筛查方法：从一个已知的质数开始排除无限多比他大的合数**

```c++
class Solution {
public:
    int countPrimes(int n) {
        vector<int> isPrime(n, 1);
        //初始化
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            if (isPrime[i]) {
                //则该数为质数
                ans += 1;
                //接下来的操作：标志其倍数为合数
                //从n^2开始标记！(对于倍数kn,k<n,一定有数k为质数，在之前被标记过)
                if ((long long)i * i < n) {
                    for (int j = i * i; j < n; j += i) {
                        isPrime[j] = 0;
                    }
                }
            }
        }
        return ans;
    }
};

```

### 3.线性筛	O(n)

![Linear Time Complexity](1.png)

x=k*primes[i]，则对于数x\*primes[i+1]=k\*primes[i]\*primes[i+1],可以在primes[i+1]遍历的时候取到

```c++
class Solution {
public:
    int countPrimes(int n) {
        vector<int> primes;
        vector<int> isPrime(n, 1);
        for (int i = 2; i < n; ++i) {
            if (isPrime[i]) {
                primes.push_back(i);
            }
            for (int j = 0; j < primes.size() && i * primes[j] < n; ++j) {
                isPrime[i * primes[j]] = 0;
                if (i % primes[j] == 0) {
                    break;
                }
            }
        }
        return primes.size();
    }
};
```

## 2.数组输出：平方矩阵问题

![Question 2](2.png)

思路：**最基本的数组输出：**两层for循环嵌套（通过外层循环每执行一次回车跳转到下一行进行输出）

### 解法一：曼哈顿距离+坐标法

```c++
#include <iostream>
#include <cmath>
#include <algorithm>
using namespace std;
void printarray(int n);
int main(){
    int n;
    cin>>n;
    while(n!=0){
        printarray(n);
        cout<<endl;
        cin>>n;
    }
    return 0;
}
void printarray(int n){
    double maxi;
    int list[n][n];
    if(n%2==1){
        maxi=(n+1)/2;
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                list[i][j]=int(maxi-max(abs((n-1)/2-i),abs((n-1)/2-j)));
                //核心代码：对应位置上的数和其到中心点的曼哈顿距离有关！
                cout<<list[i][j]<<" ";
            }
            cout<<endl;
        }
    }else{
        maxi=n/2;
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                list[i][j]=int(maxi+0.5-max(abs(double(n-1)/2-i),abs(double(n-1)/2-j)));
                //偶数的情况不存在唯一的中心点，但中心点的作用只是通过曼哈顿距离的坐标表示来确定对应位置上的数的值。故可以引入一个虚拟中心点（浮点数：(n-1)/2）
                cout<<list[i][j]<<" ";
            }
            cout<<endl;
        }
    }
}
```

### 优化解法：不再依赖中心点，直接根据坐标输出值

> ①通过观察回字形矩阵, 矩阵关于对角线是左上方和右下方对称的
> ②利用二维行列循环, **获取行列+1的最小值(即min(i + 1, j + 1))**, 可得如下图形(例如n == 4):
> 1 1 1 1
> 1 2 2 ~~2~~
> 1 2 ~~3 3~~
> 1 ~~2 3 4~~
> 可以看出未划横线部分（左上部分）满足题解,此时如果使图像沿着对角线翻转,再重合,即可求解答案
> ③翻转图像,采用min(n - i, n - j)即可, 得到图像如下(例如n == 4):
> ~~4 3 2~~ 1
> ~~3 3~~ 2 1
> ~~2~~ 2 2 1
> 1 1 1 1
> ④进行图像的重合, 对应位置取最小值即可求解min(Left上, right下)



> ```C++
> #include <iostream>
> #include <cmath>
> 
> using namespace std;
> 
> int main(){
>  int n;
> while (cin >> n, n){
>  for (int i = 0; i < n; i ++){
>      for(int j = 0; j < n; j ++){
>          cout << min(min(i + 1, j + 1), min(n - i, n - j)) << " ";
>          //通过一个min（）,输出对应的数字
>      }
>      cout << endl ;
>  }
>  cout << endl;
> }
> 
> return 0;
> ```

### 方法二：**蛇形矩阵**求解

```C++
#include <iostream>
#include <cstring>
#include <cmath>

using namespace std;

const int N = 100 + 10;
int m[N][N];

int main(){
    int n;
    int dx[] = {-1, 0, 1, 0}, dy[] = {0, 1, 0, -1};
    //每个向量代表向不同方向移动

    while(cin >> n, n ){
        memset(m, 0, sizeof m);//初始化数组
        int d = 1, x = 0, y = 0;
        int cnt = 0;  // 表示改变方向次数
        int res = 1;  // 回形当前圈数
        for (int i = 0; i < n * n; i ++){
            //i是一个计数器，代表一共输出i个数
            int a = x + dx[d], b = y + dy[d];
            //(x,y) is the previous point,and (a,b)is the point that has moved
            //一个很关键的点：(a,b)相当于探路的坐标，并不会直接赋值给(x,y)。一旦if语句发现(a,b)异常(例如需要拐弯或进入小循环)，则会重新调整方向后再赋值给x,y
            m[x][y] = res;

            if (a < 0 || a >= n || b < 0 || b >= n || m[a][b]){
                //可能的需要改变方向的情况：走到底需要拐弯，走到头(m[a][b]已经被填充，输出一个非0值)
                d = (d + 1) % 4;
                //d的改变：1——2——3——4
                a = x + dx[d], b = y + dy[d];
                cnt ++;
                if (!(cnt % 4)) res ++;
                //当cnt==4时，代表转完了一圈，则进入小圈中
            }
            x = a, y = b;
        }

        for (int i = 0; i < n; i ++){
            for (int j = 0; j < n; j ++)
                cout << m[i][j] << ' ';
            cout << endl;
        }
        cout << endl;
    }

    return 0;
}

```

## 3.斐波那契数列

### （1）基本的递归做法

```C++
#include <iostream>
using  namespace std;
int Fib(int x);
int main (){
    int n,m;
    cin>>n;
    for(int i=0;i<n;i++){
        cin>>m;
        cout<<Fib(m)<<endl;
    }
    return 0;
}
int Fib(int x){
    if(x==1||x==2){
        return 1;
    }else{
        return Fib(x-1)+Fib(x-2);
    }
}
```

### （2）方法优化

```C++
#include <stdio.h>
using namespace std;
int t;
int main()
{
  cin>>t;
  while(t--)
  {
    int n;
    cin>>n;
    long long number=0,numberfront=1,numberfrofront;
    for(int i=0; i<=n; i++)
    {
      if (i==n){
          printf("Fib(%d) = %lld\n",n,number);
      }
      numberfrofront=number+numberfront;
      number=numberfront;
      numberfrofront=numberfront;
      //相当于做迭代，不断向前进（比递归的时间复杂度要低）
    }
  }
  return 0;
}
```

·此处也可以使用vector数组存储生成的每一项，可减少计算的复杂（不用每次函数调用的时候都要重新计算一遍）

## 4.ACwing 只出现一次的字符

题目描述
给你一个只包含小写字母的字符串。

请你判断是否存在只在字符串中出现过一次的字符。

如果存在，则输出满足条件的字符中位置最靠前的那个。

如果没有，输出 no。

输入格式
共一行，包含一个由小写字母构成的字符串。

数据保证字符串的长度不超过 100000

输出格式
输出满足条件的第一个字符。

如果没有，则输出 no。

### 思路：构建一种映射的关系

直接使用两个数组构建映射的关系（前提是数组的长度已知）

```C++
#include <iostream>
#include <string>
using namespace std;
int main(){
    string test;
    cin>>test;
    int list[1000]={0};
    for(auto i:test){
            list[i]++;
    }
    for(auto i:test){
        //注意这里遍历不是按list的顺序进行遍历，而是还是按照test的值(不同字符的ASCII码)来进行遍历，确保输出的始终是最大且最靠前的一项。
        if(list[i]==1){
            cout<<i;
            goto end;
        } 
    }
    cout<<"no";
    end:
    return 0;
}
```

## 5.ACwing 字符串中最长的连续出现的字符

求一个字符串中最长的连续出现的字符，输出该字符及其出现次数，字符串中无空白字符（空格、回车和 tabtab），如果这样的字符不止一个，则输出第一个。

**输入格式**

第一行输入整数 NN，表示测试数据的组数。

每组数据占一行，包含一个不含空白字符的字符串，字符串长度不超过 200200。

**输出格式**

共一行，输出最长的连续出现的字符及其出现次数，中间用空格隔开。

#### 解法①：最基本的思路

- 使用前后指针和flag判断连续字符
- 使用countlist数组记录每个字符连续出现的最大值
- 使用vector数组successful按顺序储存连续出现的字符，并且在其值更新时自动移动到序列尾端

```C++
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
using namespace std;
void findthelongest(){
    int countlist[300]={0};
    vector <char> successful;
    int flag=0;
    int tempcount=0;
    string teststring;
    cin>>teststring;
    for(int i=0;i<teststring.length()-1;i++){
        if(teststring[i]==teststring[i+1]){
            flag=1;
            if(find(successful.begin(),successful.end(),teststring[i])==successful.end()){
                successful.push_back(teststring[i]);
            }
            
        }
        else{
            flag=0;
            if(tempcount>countlist[teststring[i]]){
                countlist[teststring[i]]=tempcount;
                successful.erase(remove(successful.begin(),successful.end(),teststring[i]));
                successful.push_back(teststring[i]);
            }
            tempcount=0;
        }
        if(flag==1){
            tempcount++;
            if(i==teststring.length()-2){
                if(tempcount>countlist[teststring[i]]){
                countlist[teststring[i]]=tempcount;
                successful.erase(remove(successful.begin(),successful.end(),teststring[i]));
                successful.push_back(teststring[i]);
                }
            }
        }
    }
    int max=0;
    char maxchar=teststring[0];
    if(!successful.empty()){
        for(auto tes:successful){
        if(countlist[tes]>max){
            maxchar=tes;
            max=countlist[tes];
        }
    }
    }
    cout<<maxchar<<" "<<max+1<<endl;
}
int main(){
    int N;cin>>N;
    for(int i=0;i<N;i++){
        findthelongest();
    }
    return 0;
}
```

### 解法②可移动的双指针

解法优化：

- 使用可移动的双指针ij，每次只有j向前不断移动直到遇到不同的元素，i最后追上j
- 适应覆盖的思想而不使用指针存储

```C++
#include <iostream>
using namespace std;
int main()
{
    int T;
    cin >> T;
    while(T --)
    {
        int maxn = -1;//maxn记录最大长度
        string str, maxs;//maxs记录最大长度时的字符
        cin >> str;
        for(int i = 0; i < str.size();)
        {
            int j = i;
            int cnt = 0;
            while(str[j] == str[i] && j < str.size())//当指针j没有越界且与指针i的内容相同时移动
                j ++, cnt ++;
            if(cnt > maxn)//更新最大值
                maxn = cnt, maxs = str[i];
            i = j ;//移动指针i
        }
        cout << maxs << " " << maxn << endl;
    }
}
```

