---
title: DataStructure-Stack-Queue-Advanced
date: 2025-03-16 18:39:53
index_img: /img/cover/increasingstack.jpg
excerpt: Several advanced usage of stack, including the increasing stack, increasing queue and the recursive simulation of stack.
math: true
categories:
  - Algorithm
  - SJTU CS0501(H) Data Structure
tags:
  - tutorial
  - Stack
  - Queue
  - Finished
  - BFS/DFS
  - Data Structure
  - Increasing stack
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Stack and Queue: Advanced

## Introduction

- Increasing / Decreasing stack
- Increasing / Decreasing queue
- simulation of recursion using stack

## Increasing Stack

**基本思路**：在线性扫描一个数组的时候，维护一个单调栈（可以是单调递增的，也可以是单调递减的，甚至可以是自己定义的一些`compare_function`）。以单调递减栈为例，为了保证单调性不被破坏，我们需要做一下操作：

- 首先将第一个元素入栈，all is well
- 接着讲新的元素不断入栈：
	- 如果栈的**单调递减性质**未被打破，那么正常入栈
	- 如果栈的**单调递减性质**被打破，意味着**将要入栈的元素小于栈顶元素**：
		- 那就开始弹出栈顶元素，直到变成情况1！
- 完成所有的扫描工作

经过$O(n)$的线性扫描之后，我们可以得到这样的性质：

- 对于将要入栈的元素，我们可以找到**第一个比该元素小的元素**即为栈顶元素。

	- 因为所有比该元素大的元素都会储存在栈中。

	> 这一条并不常用，也可以采用反向再遍历一遍的笨方法。

- 对于栈顶元素，我们可以找到**第一个比栈顶元素大的**的元素即为那个导致其被弹出的入栈元素。

	- 也就是在该元素（栈顶元素）右边第一个大于栈顶元素的元素索引！

	

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-03-23 11:47:09
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-03-23 15:04:18
 * @FilePath: /20250316_stack_and_queue/template_stack.cpp
 * @Description: Template of increasing stack
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */

/*
template of increasing stack
*/

#include <iostream>

int main(){
    int size = 10;
    //the list that needs to be scanned
    int *list = new int[size];

    for(int i = 0; i < size; ++i){
        std::cin >> list[i];
    }

    //the simulation of stack using traditional array
    //the stack stores the index of several numbers, but not the value of them!
    int *st = new int[size];
    int top = 0;
    st[top] = 0;
    
    //模版目标：找到每一个元素后面第一个比其大的元素，如果不存在则为-1
    int *ans = new int[size];

    /*
    在此处使用了单调递减的栈，可以找到其后面第一个比其大的元素
    同理，使用单调递增栈，可以找到其后面第一个比其小的元素

    当然也可以自定义其他的比较函数，那么while循环里面的内容就需要更改
    */
    for(int i = 1; i < size; i++){
        //the stack is not empty and maintain the increasing sequence
        while(top >= 0 && list[st[top]] < list[i]){
            //now the top of the stack has been poped, which means the first element that is bigger than the top index has appeared!
            ans[st[top]] = i;
            top --;
        }

        //if the stack has been cleaned, then top = -1, doens't lead to contradiction
        //push new element
        st[++ top] = i;
    }

    //for the elemnet remaining, there is no element backwards which is bigger than it.
    while(top >= 0){
        ans[st[top --]] = -1;
    }
    

    //check the answers
    for(int i = 0; i < size; ++i){
        std::cout << list[i] << " ";
    }
    std::cout << std::endl;
    for(int i = 0; i < size; ++i){
        std::cout << ans[i] << " ";
    }
    

    delete[] list;
    delete[] st;
    delete[] ans;
}

```

## Increasing Queue

在单调栈的基础限定之上加上了**滑动窗口**：

- 从栈变成队列，元素的进入和出去不再在同一个位置.
- 规定滑动窗口的长度不可以大于$m$.

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-03-23 11:47:09
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-03-23 15:39:34
 * @FilePath: /20250316_stack_and_queue/template_queue.cpp
 * @Description: template of increasing queue
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */

/*
Template of Increasing Queue
*/
#include <iostream>
int main(){
    int size = 10;
    int windows_size = 3;
    //the list that needs to be scanned
    int *list = new int[size];

    for(int i = 0; i < size; ++i){
        std::cin >> list[i];
    }

    //the simulation of queue using traditional array
    int *qu = new int[windows_size + 1];
    int left_index = 0, right_index = 0;
    
    //模版目标：找到每一个元素后面第一个比其大的元素，如果不存在则为-1
    int *ans = new int[size - windows_size + 1];

    
    /*
    此处使用单调递减的队列，可以用来求得每一个滑动窗口中的最小值
    此处left_index是出队列的地方：
        1.如果新加入的元素超过了windows的限制
    */
    for(int i = 1; i < size; i++){
        //exceed the window
        while(left_index <= right_index && qu[left_index] <= i - windows_size){
            ++ left_index;
        }

        // maintain the decreasing size of the queue
        while(left_index <= right_index && list[qu[right_index]] < list[i]){
            -- right_index;
        }
        //此处和单调栈很类似，为了维护单调性，我们需要pop掉一些不满足单调性的元素（此处是为了满足单调递减的性质）

        qu[++right_index] = i;

        if(i >= windows_size - 1){
            ans[i - windows_size + 1] = list[qu[left_index]];
        }
    }


    //check the answers
    for(int i = 0; i < size; ++i){
        std::cout << list[i] << " ";
    }
    std::cout << std::endl;
    for(int i = 0; i < size - windows_size + 1; ++i){
        std::cout << ans[i] << " ";
    }
    

    delete[] list;
    delete[] qu;
    delete[] ans;
}

```

### Example

**单调队列**最经典的问题就是可以求出一个滑动窗口的最小值，并且在$O(n)$的时间复杂度下便可完成整个数组的扫描和求解。例如下面的题目：

给定一个数组，求最大的子数组的区间求和，在此处规定**子数组的长度不超过`limit`**。

解决思路：**前缀和 + 单调队列**。

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-03-20 19:46:36
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-03-20 23:42:42
 * @FilePath: /20250316_stack_and_queue/1798.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
using namespace std;

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

long long total_trees, max_length;
int *tree_height;


int main(){
    total_trees = read();
    max_length = read();
    tree_height = new int[total_trees];
    long long ans = -9000000000000;
    long long* q = new long long [total_trees];
    long long* prefix_sum = new long long [total_trees + 1]();

    long long left_index = 0, right_index = 0;
    for(int i = 0; i < total_trees; ++i){
        tree_height[i] = read();
        prefix_sum[i + 1] = prefix_sum[i] + tree_height[i];
        while(left_index <= right_index && i - q[left_index] > max_length){
            left_index++;
        } 
        while(left_index <= right_index && prefix_sum[i] < prefix_sum[q[right_index]]){
            -- right_index;
        }

        q[++ right_index] = i;
        ans = max(ans, prefix_sum[i] - prefix_sum[q[left_index]]);

    }
    printf("%lld", ans);
    
}
```

再来一个例子！

### Example2

给定一个数组，并且规定一个区间$[S,T]$，求一个连续子数组，满足：

- 子数组的长度在该区间中
- 子数组的平均值为最大

只要求精读保持到小数点后三位。

此处在使用**单调队列**之前还需要一些预处理的工作，在这里使用**二分答案**来寻找精度解。而单调队列就是来找是否存在符合条件的子数组使其平均值大于当前的二分查找值。

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-03-23 20:48:17
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-03-23 22:10:53
 * @FilePath: /Test2/2_advance.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
#include <iomanip>
using namespace std;


bool isValid(double mid, int n, int S, int T, int* a) {

    bool ans = false;
    double* prefix = new double [n + 1]();

    for (int i = 1; i <= n; ++i) {
        prefix[i] = prefix[i - 1] + (a[i - 1] - mid);
    }
    /*
    prefix 是维护前缀和的数组，此处对数组归一化方便计算
    */


    //using the increase queue
    int* qu = new int[n + 1];
    
    //the simulation of queue using traditional array
    int left_index = 0, right_index = -1;
    int windows_size = T - S;

    
    /*
    此处使用单调递减的队列，可以用来求得每一个滑动窗口中的最小值
    此处left_index是出队列的地方：
        1.如果新加入的元素超过了windows的限制
    */
    for(int j = S; j <= n; ++j){
        //j represents the right index
        //exceed the window

        /*
        在给定右端点j的情况下，我们需要找到i的prefix最小值，得到求和的最大值，在判断是否存在
        i此时可以转化为一个滑动窗口的问题：
        j - T <= i <= j - S，窗口大小为T-S
        正向遍历i的可能取值，使用单调队列的思想
        */

        
        while(left_index <= right_index && qu[left_index] < j - T){
            ++ left_index;
        }

        // maintain the decreasing size of the queue
        while(left_index <= right_index && prefix[qu[right_index]] > prefix[j - S]){
            -- right_index;
        }
        //此处和单调栈很类似，为了维护单调性，我们需要pop掉一些不满足单调性的元素（此处是为了满足单调递减的性质）

        qu[++right_index] = j - S;

        if(prefix[j] - prefix[qu[left_index]] >= 0){
            ans = true;
            break;
        }
    }

    delete[] prefix;
    delete[] qu;
    return ans;
}

// 主函数
int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    std::cout.tie(nullptr);
    int n, S, T;
    cin >> n >> S >> T;
    

    int * a = new int[n];
    for (int i = 0; i < n; ++i) {
        cin >> a[i];
    }

    // 二分答案
    double low = -1e6, high = 1e6;
    double precision = 1e-5; 
    while (high - low > precision) {
        double mid = (low + high) / 2.0;
        if (isValid(mid, n, S, T, a)) {
            low = mid;
        } else {
            high = mid;
        }
    }

    cout << fixed << setprecision(3) << low;

    return 0;
}
```



## Simulating Recursion

在这里使用栈模拟递归的调用：

**递归的调用**的核心在于栈帧空间，因此可以手动使用栈来模拟。那么这些**栈帧空间**需要维护哪些内容？

- **元素的状态**
- 这一层函数调用的**所有参数列表**
- 这一层函数调用的**返回值**

{% note primary %}

1.执行代码块0

2.保存现场准备进入下一层

3.接受下层返回的数据

4.恢复现场

5.继续执行代码块1

{% endnote %}

**Example**：给出如下函数调用的示例：

$$f(0) =f(1)=1,\ g(0) = g(1) =1$$

$$f(n)=f(n-1)+g(n-2),\ g(n)=f(n-1)+g(n-1)$$

```cpp
#include <stack>
#include <iostream>
using namespace std;

typedef char func;

struct node
{
    func fu;
    int n;//the input
    int pc;//the status
    int fr, gr;//the return value
    node(func f, int n, int p, int fr, int gr):fu(f), n(n), pc(p), fr(fr), gr(gr){}
};


int computefunc(func which_func, int input){
    stack <node> frame;
    int ret = 0;
    frame.push(node(which_func, input, 1, 0, 0));
    while(!frame.empty()){
        auto& top = frame.top();
        switch (top.fu){
            case 'f':
                switch (top.pc){
                    case 1:
                        if(top.n <= 1){
                            ret = 1;
                            frame.pop();
                        }else{
                            top.pc++;
                            frame.push(node('f', top.n - 1, 1, 0 ,0));
                        }
                        break;
                    case 2:
                        top.pc ++;
                        top.fr = ret;
                        frame.push(node('g', top.n - 2, 1,0,0));
                        break;
                    case 3:
                        ret += top.fr;
                        //此时f和g的子函数返回值全部已经计算完毕，ret来返回到上一层的栈帧空间
                        frame.pop();
                        break;
                }break;
            case 'g':
                switch(top.pc){
                    case 1:
                        if(top.n <= 1){
                            ret = 1;
                            frame.pop();
                        }else{
                            top.pc++;
                            frame.push(node('g', top.n - 1, 1, 0 ,0));
                        }
                        break;
                    case 2:
                        top.pc ++;
                        top.gr = ret;
                        frame.push(node('f', top.n + 1, 1,0,0));
                        break;
                    case 3:
                        ret += top.gr;
                        frame.pop();
                        break;
                }break;
        }
    }
    return ret;
}


int main(){
    cout << computefunc('f', 5);
}
```

在这里**最重要的几点**：

- **栈帧状态的设计**：

	- 1：最原始的栈帧状态
	- 2：进入2代表已经得到第一个（+号左边）的子函数的返回值
	- 3：进入3代表已经得到第二个（+号右边）的子函数的返回值

- 有一个变量`ret`， 这个变量是程序成功的核心，代表上一层栈帧的**返回值**，通过`ret`会更新当前栈帧的相关参数。

	- 当栈被清空之后，很有趣！此时ret就是主函数的返回值。（因为主函数的第一个状态就是栈帧的最底层）


### Example

**理论上**，任何递归函数都可以使用**栈的模拟递归**来实现，只不过是实现复杂度的问题，我们只是**替代**了编译器帮我们完成的工作，而且，我们可以在**堆内存**上实现，防止**爆栈**。

在此处给出一道经典例题：二叉树的后根遍历（其递归实现略）

题目链接：[OJ 2595](https://acm.sjtu.edu.cn/OnlineJudge/problem/2595)

给定一棵二叉树，以栈模拟递归的方式返回其节点值的**后序遍历**。

```cpp
#pragma once
#ifndef SRC_HPP
#define SRC_HPP

/*
The recursive implementation:
traverse(x.left)
traverse(x.right)
traverse(x)

the default function
if(node.left == nullptr && node.right == nullptr){
    traverse the node of x
}


*/

class Node;
struct Info {
  // Add whatever you want here
  int current_size; // now the traversa has gone to which section
  int pc;
  //pc = 1: The initial statement
  //pc = 2: Has been traversed the left node
  //pc = 3: has been traversed the right node
  const Node*  root_node;
  int *arr;
  int total_treesize;
  




  Info(int current= 0, int pc= 1, const Node*  root= nullptr, int *arr =nullptr, int total_treesize = 0)
  :current_size(current), pc(pc),root_node(root), arr(arr),total_treesize(total_treesize) {
    // TODO:
  }

  ~Info() {
    // TODO:
  }
};

class Node {  // DO NOT edit this part
 private:
  int _data;
  Node *_lson, *_rson;
  mutable Info _info;  // custom data

  friend Node *genTree();

 public:
  Node();

  Node(int data);

  ~Node();

  int data() const;
  const Node *const lson() const;
  const Node *const rson() const;
  Info &info() const;
};

void traversePostOrder(const Node *const root, int *arr, int tree_size) {
  // TODO
  int currentsize = 0;
  Info* st = new Info[tree_size];//the simulation of stack
  int top = 0;
  st[top] = Info(0, 1, root, arr, tree_size);

  while(top >= 0){
    auto& top_val = st[top];
    switch(top_val.pc){
        case 1:
            if(top_val.root_node->lson() == nullptr && top_val.root_node->rson() == nullptr){
                //fill in the root node!
                arr[currentsize] = top_val.root_node->data();
                currentsize ++;
                if(currentsize > tree_size){
                    return;
                }
                top --;
            }
            else if(top_val.root_node->lson() == nullptr){
                top_val.pc = 3;
                st[++top] = Info(currentsize, 1, top_val.root_node->rson(), arr, tree_size);
            }else{
                top_val.pc = 2;
                st[++top] = Info(currentsize, 1, top_val.root_node->lson(), arr, tree_size);
            }break;
        case 2:
            if(top_val.root_node->rson() == nullptr){
                arr[currentsize] = top_val.root_node->data();
                currentsize ++;
                if(currentsize > tree_size){
                    return;
                }
                top --;
            }
            else{
                top_val.pc = 3;
                st[++top] = Info(currentsize, 1, top_val.root_node->rson(), arr, tree_size);
            }break;
        case 3:
            arr[currentsize] = top_val.root_node->data();
                currentsize ++;
                if(currentsize > tree_size){
                    return;
                }
                top --;
            break;
    }
  }
}

#endif
```

