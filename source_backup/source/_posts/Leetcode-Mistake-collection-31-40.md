---
title: Leetcode-Mistake-collection-31-40
date: 2024-12-10 13:07:20
index_img: /img/cover/Leetcode.png
excerpt: Leetcode-Mistake-Collection-Episode-4, focusing on advanced algorithm.
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

# Leetcode-Mistake-collection-31-40

# 程设错题 31-35 20241112

## 1 上机错题 寻找字符串的最大非重复子串问题

#### 解法1基本双循环枚举（略）

#### 解法2 使用双指针

```C++
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;
int main(){
    string theinput;cin>>theinput;
    int i=0,j=0;
    int maxoflength=0;
    unordered_map<char,int> list;

    for(;j<theinput.length();j++){
        if(list.find(theinput[j])!=list.end()){
            i=list[theinput[j]]+1;
            if(j-i+1>maxoflength){
                maxoflength=j-i+1;
            }
        }
        list[theinput[j]]=j;
    }
    cout<<maxoflength;
    return 0;
}
```

## 2 Leetcode 888 公平糖果交换

爱丽丝和鲍勃拥有不同总数量的糖果。给你两个数组 `aliceSizes` 和 `bobSizes` ，`aliceSizes[i]` 是爱丽丝拥有的第 `i` 盒糖果中的糖果数量，`bobSizes[j]` 是鲍勃拥有的第 `j` 盒糖果中的糖果数量。

两人想要互相交换一盒糖果，这样在交换之后，他们就可以拥有相同总数量的糖果。一个人拥有的糖果总数量是他们每盒糖果数量的总和。

返回一个整数数组 `answer`，其中 `answer[0]` 是爱丽丝必须交换的糖果盒中的糖果的数目，`answer[1]` 是鲍勃必须交换的糖果盒中的糖果的数目。如果存在多个答案，你可以返回其中 **任何一个** 。题目测试用例保证存在与输入对应的答案。

#### 解法1 双循环枚举

```C++
class Solution {
public:
    vector<int> fairCandySwap(vector<int>& aliceSizes, vector<int>& bobSizes) {
        long long sum1=0,sum2=0;
        for(auto alice:aliceSizes){
            sum1+=alice;
        }
        for(auto bob:bobSizes){
            sum2+=bob;
        }
        int target=abs(sum1-sum2)/2;
        vector<int> ans;
        for(auto alice:aliceSizes){
            for(auto bob:bobSizes){
                if(sum1>sum2&&alice-bob==target){
                    ans.push_back(alice);
                    ans.push_back(bob);
                    return ans;
                }
                if(sum1<sum2&&bob-alice==target){
                    ans.push_back(alice);
                    ans.push_back(bob);
                    return ans;
                }
            }
        }
        return {};
    }
};
```

#### 解法2 使用哈希表存储，优化查找方案

```C++
class Solution {
public:
    vector<int> fairCandySwap(vector<int>& aliceSizes, vector<int>& bobSizes) {
        int sumA = accumulate(aliceSizes.begin(), aliceSizes.end(), 0);
        int sumB = accumulate(bobSizes.begin(), bobSizes.end(), 0);
        int delta = (sumA - sumB) / 2;
        //这里差值可以取负值，就不用分类讨论
        unordered_set<int> rec(aliceSizes.begin(), aliceSizes.end());
        //对Alice的数组创建哈希表（自动删去重复元素）
        vector<int> ans;
        for (auto& y : bobSizes) {
            int x = y + delta;
            if (rec.count(x)) {
                //如果找到了，就说明存在
                ans = vector<int>{x, y};
                break;
            }
        }
        return ans;
    }
};

```

## 3 Leetcode 473 火柴拼正方形

你将得到一个整数数组 `matchsticks` ，其中 `matchsticks[i]` 是第 `i` 个火柴棒的长度。你要用 **所有的火柴棍** 拼成一个正方形。你 **不能折断** 任何一根火柴棒，但你可以把它们连在一起，而且每根火柴棒必须 **使用一次** 。

如果你能使这个正方形，则返回 `true` ，否则返回 `false` 。

### 补充：DFS（深度优先搜索）的C++代码实现

DFS的关键：优先搜索点的未被访问的邻居节点

非递归的代码示例

```C++
#include <iostream>
#include <vector>
#include <stack>

using namespace std;

void dfs(int start, const vector<vector<int>>& graph, vector<bool>& visited) {
    // 使用一个栈来模拟递归
    stack<int> s;
    s.push(start);
    //将起始点start压入栈中

    while (!s.empty()) {
        // 从栈顶取出一个节点
        int node = s.top();
        s.pop();

        // 如果节点未被访问过
        if (!visited[node]) {
            // 标记为已访问
            visited[node] = true;
            
            // 打印当前访问的节点
            cout << "Visiting node: " << node << endl;

            // 将该节点的所有未访问的邻居节点压入栈中
            for (int i = graph[node].size() - 1; i >= 0; --i) {
                if (!visited[graph[node][i]]) {
                    s.push(graph[node][i]);
                }
            }
        }
    }
}

int main() {
    // 示例图的邻接表表示
    vector<vector<int>> graph = {
        {1, 2},      // 节点0的邻居节点是1和2
        {0, 3, 4},   // 节点1的邻居节点是0、3和4
        {0, 5},      // 节点2的邻居节点是0和5
        {1, 4},      // 节点3的邻居节点是1和4
        {1, 3},      // 节点4的邻居节点是1和3
        {2}          // 节点5的邻居节点是2
    };

    int numNodes = graph.size();
    vector<bool> visited(numNodes, false);

    // 从节点0开始DFS
    dfs(0, graph, visited);

    return 0;
}

```

优化为递归的版本：

```C++
void dfs(int node, const vector<vector<int>>& graph, vector<bool>& visited) {
    if (visited[node]) return;
    visited[node] = true;
    cout << "Visiting node: " << node << endl;
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            dfs(neighbor, graph, visited);
            //遍历每一个未被访问的邻居节点实现全局搜索
        }
    }
}
```



### BFS 广度优先搜索

下面是使用C++实现的BFS的示例代码，该代码会遍历一个图，并打印每个节点的访问顺序：

```cpp
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

void bfs(int start, const vector<vector<int>>& graph, vector<bool>& visited) {
    // 使用队列来存储待访问的节点
    queue<int> q;
    
    // 将起始节点标记为已访问并入队
    visited[start] = true;
    q.push(start);

    while (!q.empty()) {
        // 从队列中取出一个节点
        int node = q.front();
        q.pop();

        // 打印当前访问的节点
        cout << "Visiting node: " << node << endl;

        // 遍历当前节点的所有邻居节点
        for (int neighbor : graph[node]) {
            // 如果邻居节点未被访问过，标记为已访问并入队
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}

int main() {
    // 示例图的邻接表表示
    vector<vector<int>> graph = {
        {1, 2},      // 节点0的邻居节点是1和2
        {0, 3, 4},   // 节点1的邻居节点是0、3和4
        {0, 5},      // 节点2的邻居节点是0和5
        {1, 4},      // 节点3的邻居节点是1和4
        {1, 3},      // 节点4的邻居节点是1和3
        {2}          // 节点5的邻居节点是2
    };

    int numNodes = graph.size();
    vector<bool> visited(numNodes, false);

    // 从节点0开始BFS
    bfs(0, graph, visited);

    return 0;
}
```

1. **图的表示**: 这里使用邻接表来表示图，每个节点的邻居节点存储在一个`vector`中。

2. **BFS函数**：
   - `bfs`函数接受起始节点`start`，图`graph`，以及一个用于标记节点是否已访问的`visited`数组。
   - 使用一个队列`queue`来模拟BFS的层级访问过程。
   - 将起始节点标记为已访问并入队。
   - 当队列不为空时，弹出队列的第一个节点（FIFO），打印该节点，然后将其所有未访问的邻居节点标记为已访问并入队。

3. **主函数**：
   - 定义了一个示例图。
   - 创建一个`visited`数组来跟踪每个节点的访问状态。
   - 从节点0开始调用BFS函数。

这个示例展示了如何使用队列来实现BFS。如果你更喜欢使用递归来实现BFS，可以使用一个辅助函数来模拟队列的功能，但通常非递归的实现更直观和高效，因为BFS本身就是一个层级遍历的过程。

### 题解

```C++
class Solution {
public:
    bool dfs(int index, vector<int> &matchsticks, vector<int> &edges, int len) {
        if (index == matchsticks.size()) {
            return true;
        }
        for (int i = 0; i < edges.size()/*4*/; i++) {
            edges[i] += matchsticks[index];
            if (edges[i] <= len && dfs(index + 1, matchsticks, edges, len)) {
                return true;
            }
            edges[i] -= matchsticks[index];
            //算法的关键：判断条件：如果edges没有超过边界，那么尝试把第index+1根火柴添加到每一条边上
        }
        return false;
    }

    bool makesquare(vector<int> &matchsticks) {
        int totalLen = accumulate(matchsticks.begin(), matchsticks.end(), 0);
        if (totalLen % 4 != 0) {
            return false;
        }
        sort(matchsticks.begin(), matchsticks.end(), greater<int>()); 
        // 减少搜索量
        vector<int> edges(4);
        return dfs(0, matchsticks, edges, totalLen / 4);
    }
};

```

## 4 Leetcode 921 栈（LIFO）的应用

栈的“先进后出”

只有满足下面几点之一，括号字符串才是有效的：

- 它是一个空字符串，或者
- 它可以被写成 `AB` （`A` 与 `B` 连接）, 其中 `A` 和 `B` 都是有效字符串，或者
- 它可以被写作 `(A)`，其中 `A` 是有效字符串。

给定一个括号字符串 `s` ，在每一次操作中，你都可以在字符串的任何位置插入一个括号

- 例如，如果 `s = "()))"` ，你可以插入一个开始括号为 `"(()))"` 或结束括号为 `"())))"` 。

返回 *为使结果字符串 `s` 有效而必须添加的最少括号数*。

#### 解法：使用栈！

```C++
class Solution {
public:
    int minAddToMakeValid(string s) {
        stack <char> thekuohao;
        //代表一个栈，之后不断压入内容
        int count=0;
        for(auto ch:s){
            if(ch=='('){
                thekuohao.push(ch);
                //左括号希望在后面找到他唯一匹配的右括号
            }else if(ch==')'){
                if(!thekuohao.empty()&&thekuohao.top()=='('){
                    thekuohao.pop();
                    //找到了，弹出栈顶的左括号，同时不压入右括号
                }else{
                    count++;
                    //说明此时的右括号无人访问，需要在另一个方向压入新的左括号
                    //count统计到的时需要添加的左括号（即存在盈余的右括号）
                }
            }
        }
        return count+thekuohao.size();
        //最后留在栈中的括号都是未被匹配到的左括号
    }
};
```

如果降低空间复杂度O（1），则可以这样做：

```C++
class Solution {
public:
    int minAddToMakeValid(string s) {
        int ans = 0;
        int leftCount = 0;
        for (auto &c : s) {
            if (c == '(') {
                leftCount++;
            } else {
                if (leftCount > 0) {
                    leftCount--;
                    //那么右括号一定可以找到被匹配的左括号
                } else {
                    ans++;
                    //leftcount==0，说明此时应该在另一侧加入括号
                }
            }
        }
        ans += leftCount;
        return ans;
    }
};

```

## 5 Leetcode 937 自定义排序

给你一个日志数组 `logs`。每条日志都是以空格分隔的字串，其第一个字为字母与数字混合的 **标识符** 。

有两种不同类型的日志：

- **字母日志**：除标识符之外，所有字均由小写字母组成
- **数字日志**：除标识符之外，所有字均由数字组成

请按下述规则将日志重新排序：

- 所有 **字母日志** 都排在 **数字日志** 之前。
- **字母日志** 在内容不同时，忽略标识符后，按内容字母顺序排序；在内容相同时，按标识符排序。
- **数字日志** 应该保留原来的相对顺序。

返回日志的最终顺序。

**示例 1：**

```
输入：logs = ["dig1 8 1 5 1","let1 art can","dig2 3 6","let2 own kit dig","let3 art zero"]
输出：["let1 art can","let3 art zero","let2 own kit dig","dig1 8 1 5 1","dig2 3 6"]
解释：
字母日志的内容都不同，所以顺序为 "art can", "art zero", "own kit dig" 。
数字日志保留原来的相对顺序 "dig1 8 1 5 1", "dig2 3 6" 。
```

**对于较为复杂的排序问题，可以尝试使用自定义排序的方法实现排序规则的“自定义”。**

#### 最基本的例子：使用sort函数实现升序和降序数组排序

```C++
#include <iostream>
#include <vector>
#include <algorithm>

// 自定义比较函数
bool compare(int a, int b) {
    // 这里定义你的排序规则
    // 返回true表示a应该在b之前（即a较小）
    return a < b;  // 这是一个简单的升序比较
}

int main() {
    std::vector<int> numbers = {5, 2, 9, 1, 5, 6};

    // 使用自定义比较函数进行升序排序
    std::sort(numbers.begin(), numbers.end(), compare);

    std::cout << "自定义升序排序后的结果: ";
    for (const auto& num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    // 现在定义一个不同的比较函数进行降序排序
    bool descendingCompare(int a, int b) {
        return a > b;  // 返回true表示a应该在b之后（即b较小）
    }

    // 使用自定义比较函数进行降序排序
    std::sort(numbers.begin(), numbers.end(), descendingCompare);

    std::cout << "自定义降序排序后的结果: ";
    for (const auto& num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    return 0;
}

```

### 题解：

```C++
class Solution {
public:
    vector<string> reorderLogFiles(vector<string>& logs) {
        stable_sort(logs.begin(), logs.end(), [&](const string & log1, const string & log2) {
            //使用stable_sort保证相同元素排序的稳定性
            int pos1 = log1.find_first_of(" ");
            int pos2 = log2.find_first_of(" ");
            //库函数find_first_of()可以实现定位到字符串的目标字符的第一个索引位置
            
            bool isDigit1 = isdigit(log1[pos1 + 1]);
            bool isDigit2 = isdigit(log2[pos2 + 1]);
            //这两个bool值判断是否为数字，进而实现对两种不同的日志的分类
            if (isDigit1 && isDigit2) {
                return false;
                //都是数字，保持位置
            }
            if (!isDigit1 && !isDigit2) {
                //如果都不是数字
                string s1 = log1.substr(pos1);
                //复制子串的库函数 substr()
                string s2 = log2.substr(pos2);
                if (s1 != s2) {
                    //string类重载了对应运算符，可以直接实现字符串的比较(逐位比较)
                    return s1 < s2;
                }
                return log1 < log2;
                //如果内容相同，则比较日志头部log1和log2
            }
            return isDigit1 ? false : true;
            //最后一种情况，一个为数字，一个为字符，则根据规则字符应该排在前面
        });
        //以上内容都是Lambda表达式，实现对自定义排序函数compare的定义
        //在compare中，如果匿名函数返回false，说明不用交换顺序
        return logs;
    }
};

```

### 补充知识点讲解：Lambda表达式

在C++中，lambda表达式（Lambda Expressions）**是一种简洁的方式来定义匿名函数对象**（即没有名称的函数），它允许你直接在代码中定义小型的、一次性使用的函数。**这些函数对象可以捕获外部作用域中的变量**，并可以在需要时直接使用。Lambda表达式的引入使得C++编程更加灵活和方便，特别是在需要短小、临时函数时。以下是关于lambda表达式的一些关键点：

#### 基本语法

Lambda表达式的基本形式如下：

```cpp
[捕获列表](参数列表)可变性规范 -> 返回类型 {
    // 函数体
}
```

- **捕获列表**：`[]` 用于指定哪些外部变量应该被lambda表达式捕获。可以是：
  - `[]` 什么都不捕获。
  - `[=]` 按值捕获所有可见的外部变量。
  - `[&]` 按引用捕获所有可见的外部变量。
  - `[x, &y]` 按值捕获`x`，按引用捕获`y`。
- **参数列表**：类似于普通函数的参数列表，可以为空。
- **可变性规范**：`mutable`关键字表示lambda表达式可以修改被值捕获的变量。
- **返回类型**：如果lambda表达式包含多个`return`语句或需要明确指定返回类型，可以使用`->`来指定。
- **函数体**：包含lambda表达式的实际代码。

#### 例子

```cpp
#include <iostream>
#include <algorithm>
#include <vector>

int main() {
    std::vector<int> numbers = {1, 2, 3, 4, 5};
    int factor = 2;

    // 使用lambda表达式对向量中的每个元素乘以factor
    std::for_each(numbers.begin(), numbers.end(), [factor](int &n) {
        n *= factor;
    });

    // 打印结果
    for (const auto& num : numbers) {
        std::cout << num << " ";
    }
    std::cout << std::endl;

    // 另一个lambda表达式示例，返回一个函数
    auto add = [](int x, int y) -> int {
        return x + y;
    };

    std::cout << "1 + 2 = " << add(1, 2) << std::endl;

    return 0;
}
```

#### 特点

- **匿名性**：Lambda表达式是匿名的，不需要为其定义一个名称。
- **捕获外部变量**：可以捕获外部作用域的变量，允许这些变量在lambda表达式内使用。
- **简洁性**：相比于定义一个函数对象或函数指针，lambda表达式更加简洁。
- **闭包**：lambda表达式可以创建闭包，捕获的变量在lambda表达式执行时仍然有效。

Lambda表达式在C++11及以后的标准中被引入，极大地方便了编写简短的函数式代码，常用于STL算法、事件处理、异步编程等场景。

### 补充知识点讲解：深入了解sort函数

`std::sort`在C++标准库中实现的是**内省排序（Introsort）**，这是一种混合排序算法，结合了快速排序（Quicksort）、堆排序（Heapsort）和插入排序（Insertion Sort）的优点。以下是`std::sort`的基本原理：

#### 1. **快速排序（Quicksort）**

- `std::sort`首先尝试使用快速排序，因为快速排序在平均情况下有很好的性能，时间复杂度为O(n log n)。
- 快速排序通过选择一个枢轴元素（pivot）将数组分成两部分，比枢轴小的元素放在一侧，比枢轴大的元素放在另一侧，然后递归地对这两部分进行排序。

#### 2. **堆排序（Heapsort）**

- 当快速排序的递归深度超过一定阈值时（通常是log n），`std::sort`会切换到堆排序。
- 堆排序保证了最坏情况下的时间复杂度为O(n log n)，避免了快速排序在某些情况下可能退化到O(n^2)的性能。

#### 3. **插入排序（Insertion Sort）**

- 对于小规模的数据（通常是少于16个元素的子数组），`std::sort`会使用插入排序，因为在小数据集上，插入排序的性能通常优于其他排序算法。

#### 特点

- **自适应性**：`std::sort`根据数据的大小和递归深度自动选择最适合的排序策略，这使得它在各种情况下都表现良好。
- **稳定性**：`std::sort`不是稳定的排序算法，这意味着相同元素的相对顺序在排序后可能改变。如果需要稳定排序，可以使用`std::stable_sort`。
- **内存使用**：`std::sort`通常是原地排序算法，意味它不需要额外的内存空间（除了递归调用栈），这对大规模数据的排序是非常有利的。

由于C++标准库的具体实现可能会因不同的编译器和标准库版本而有所不同，这里提供一个简化的、概念性的`std::sort`实现，展示内省排序的核心思想：

```cpp
#include <algorithm>
#include <functional>
#include <vector>

namespace std {
    template<class RandomIt, class Compare = std::less<>>
    void sort(RandomIt first, RandomIt last, Compare comp = Compare()) {
        // 内省排序的阈值，通常为log(n)
        const int introsort_threshold = 2 * static_cast<int>(log(last - first) / log(2));

        // 辅助函数，执行快速排序
        auto quicksort = [&](RandomIt low, RandomIt high, int depth) {
            if (high - low < 2) return; // 已经排序或只有一个元素

            if (depth == 0) {
                // 达到最大深度，使用堆排序
                std::make_heap(low, high, comp);
                std::sort_heap(low, high, comp);
                return;
            }

            --depth;

            // 选择枢轴并进行分区
            RandomIt pivot = std::partition(low, high, 
                [&](const auto& a) { return comp(a, *std::next(high, -1)); });

            // 递归排序左右两部分
            quicksort(low, pivot, depth);
            quicksort(pivot, high, depth);
        };

        // 开始内省排序
        quicksort(first, last, introsort_threshold);

        // 对小规模数据使用插入排序
        if (last - first <= 16) {
            for (RandomIt i = first + 1; i != last; ++i) {
                auto key = *i;
                RandomIt j = i - 1;
                while (j >= first && comp(key, *j)) {
                    *(j + 1) = *j;
                    --j;
                }
                *(j + 1) = key;
            }
        }
    }
}
```

#### 解释

1. **内省排序阈值**：`introsort_threshold`用于决定何时从快速排序切换到堆排序，通常设置为`2 * log(n)`。

2. **快速排序部分**：
   - `quicksort`函数实现了快速排序的核心逻辑。
   - 使用`std::partition`进行分区，基于比较函数`comp`。
   - 当递归深度达到`introsort_threshold`时，切换到堆排序。

3. **堆排序**：
   - 当达到最大递归深度时，调用`std::make_heap`和`std::sort_heap`来完成排序。

4. **插入排序**：
   - 对于小规模数据（这里设定为16个元素或更少），使用插入排序来优化性能。

5. **比较函数**：
   - `comp`参数允许用户提供自定义的比较函数，实现自定义排序逻辑。

请注意，这是一个简化的实现，实际的标准库实现可能包含更多的优化：

- 选择枢轴的策略可能更复杂，以避免最坏情况（如已经排序或逆序的数组）。
- 可能使用不同的阈值来决定何时切换到堆排序或插入排序。
- 内存管理和性能优化，如减少递归调用的栈空间使用。
- 可能使用其他技术来减少比较次数或提高缓存命中率。

这种实现展示了内省排序的基本理念：结合快速排序的平均性能优势、堆排序的最坏情况保证，以及插入排序在小数据集上的效率。



# 程序设计错题 36-40

## 1.Leetcode 334 经典算法题：递增三元子序列排序

给你一个整数数组 nums ，判断这个数组中是否存在长度为 3 的递增子序列。

如果存在这样的三元组下标 (i, j, k) 且满足 i < j < k ，使得 nums[i] < nums[j] < nums[k] ，返回 true ；否则，返回 false。

#### 解法1 O(n^3)暴力枚举

```C++
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        if(nums.size()<3){
            return false;
        }
        for(int i=0;i+2<nums.size();i++){
            for(int j=i+2;j<nums.size();j++){
                if(nums[j]<=nums[i]){
                    continue;
                }else{
                    for(int k=i+1;k<j;k++){
                        if(nums[j]>nums[k]&&nums[k]>nums[i]){
                            return true;
                        }
                    }
                }
            }
        }
        return false;
    }
};
```

#### 解法2 动态规划（也会超时）

维护一个dp[i]**来记录每个位置的最长递增子序列的长度**

```C++
#include <vector>
#include <algorithm>

class Solution {
public:
    bool increasingTriplet(std::vector<int>& nums) {
        if (nums.size() < 3) return false;
        std::vector<int> dp(nums.size(), 1);
        
        for (int i = 1; i < nums.size(); ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[i] > nums[j]) {
                    dp[i] = std::max(dp[i], dp[j] + 1);
                    if (dp[i] >= 3) return true;
                }
            }
        }
        return false;
    }
};

```

牺牲空间复杂度，换取时间复杂度：O(n^2)和O(n)

#### 优化1 使用双向遍历

![双向遍历](1.png)

维护数组的好处：提高了空间复杂度，但是不用在每一次遍历的时候重新找存在的元素了（相当于动态规划的思想）

```C++
class Solution {
public:
    bool increasingTriplet(vector<int>& nums) {
        int n=nums.size();
        if(n<3){
            return false;
        }
        vector<int> leftnum(n,nums[0]);
        vector<int> rightnum(n,nums[n-1]);
        
        for(int i=1;i<n;i++){
            leftnum[i]=min(leftnum[i-1],nums[i]);
        }
        for(int i=n-2;i>=0;i--){
            rightnum[i]=max(rightnum[i+1],nums[i]);
        }
        for(int i=1;i<n-1;i++){
            if(nums[i]>leftnum[i-1]&&nums[i]<rightnum[i+1]){
                return true;
            }
        }
        return false;
    }
};
```

时间复杂度：O(n^2)

空间复杂度：O(n^2)

#### 优化2 贪婪算法

first 和 second 代表这个数组中我们所找到的最小值和次小值

```C++
#include <vector>
#include <climits>

class Solution {
public:
    bool increasingTriplet(std::vector<int>& nums) {
        int first = INT_MAX, second = INT_MAX;
        //定义系统的最大最小值
        for (int n : nums) {
            if (n <= first) {
                first = n;
            } else if (n <= second) {
                second = n;
            } else {//(n>first && n>second)
                return true;
                //理论分析：找到最小值和次小值为什么就可以判定true？
                //找到了n>first && n>second，则n作为三元组的最后一个数，而且根据遍历的规则，n的前面一个数一定是当前数组的second或者first！
                //如果是second，那么说明first还在前面，找到了符合条件的三元组
                //如果是first，因为这里n>second，说明second的值已经被更新过，说明在n前面，first的值至少被更新过两次，second至少被更新过一次
                //a1(数组第一个数，被更新为first)——a2(second)--a3(first)--n
                //a1和a3之间还可以有很多次second和first值的更新，a3<a1<a2<n;
            }
        }
        return false;
    }
};

```

时间复杂度O(n)，空间复杂度O(1)
