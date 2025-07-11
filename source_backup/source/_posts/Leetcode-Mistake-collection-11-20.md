---
title: Leetcode-Mistake-collection-11-20
date: 2024-12-10 13:06:52
index_img: /img/cover/Leetcode.png
excerpt: Leetcode-Mistake-Collection-Episode-2, focusing on Hashmap, unordered_map, and two pointers.
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

# Leetcode-Mistake-collection-11-20

# 程设错题 6-10 20241015-20241020

## 1.ACwing 776 字符串移位问题

对于一个字符串来说，定义一次循环移位操作为：将字符串的第一个字符移动到末尾形成新的字符串。

给定两个字符串 s1s1 和 s2s2，要求判定其中一个字符串是否是另一字符串通过若干次循环移位后的新字符串的子串。

例如 `CDAA` 是由 `AABCD` 两次移位后产生的新串 `BCDAA` 的子串，而 `ABCD` 与 `ACBD` 则不能通过多次移位来得到其中一个字符串是新串的子串。

**输入格式**

共一行，包含两个字符串，中间由单个空格隔开。

字符串只包含字母和数字，长度不超过 3030。

**输出格式**

如果一个字符串是另一字符串通过若干次循环移位产生的新串的子串，则输出 `true`，否则输出 `false`。

### 基本思路：

- 使用函数，判断一个字符串是不是另一个字符串的子串
- 利用for循环实现对目标数组的移位处理，并且对每个新移位的字符串调用该函数

```C++
#include <string>
#include <iostream>
#include <vector>
using namespace std;
bool judgestring(string teststring,string standardstring){
    if(teststring.length()<standardstring.length()){
        return false;
    }else{
        if(teststring.find(standardstring)==-1){
            return false;
        }else{
            return true;
        }
    }
}
int main(){
    string movestring,standardstring;
    cin>>movestring>>standardstring;
    
    for(int i=0;i<movestring.length()-1;){
        movestring.push_back(movestring[0]);
        movestring.erase(movestring.begin());
        if(judgestring(movestring,standardstring)){
            cout<<"true";
            goto end;
        }
    }
    cout<<"false";
    end:;
    return 0;
}

```

### 优化思路：

若将一个字符串首尾相接，则不需要进行移项操作（相当于顺序没有发生变化）

```C++
#include <iostream>
#include <string>

using namespace std;

bool check(string a, string b)
{
    int len = a.size();
    a += a; //复制字符串并连接
    //if (a.find(b) >= 0 && a.find(b) < len) return true; //判断是否包含
    if (a.find(b) != string::npos) return true; //判断a中是否包含b
    //两种判断方式，推荐第二种，若find函数未找到，则会返回一个特殊常量string::npos
    return false;
}

int main()
{
    string a, b;
    cin >> a >> b;
    if (a.size() < b.size()) swap(a, b);
    //默认a是更大的string类
    if (check(a, b)) cout << "true";
    else cout << "false";
    return 0;
}

```

## 2.ACwing 777 字符串的最小周期问题

给定一个字符串s,求其最小的周期格段

```c++
#include<bits/stdc++.h>
using namespace std;
int main()
{
    string s;
    int flag=1;
    for(int i=1;i<=s.length();++i){
        //外层循环：枚举可能的周期，从小到大枚举，第一个得到的值就是目标值
        flag=1;
        if(len%i!=0){
            continue;
        }
        for(int j=0;i<s.length();j++){
            if(s[j]!=s[j%i]){
                //核心代码:判断每一个j和j%i是否相等，判断是否符合周期性的定义。
                flag=0;
                break;
            }
            if(flag){
                cout<<s.length()/i<<endl;
                goto :end;
            }
        }
        cout<<"-1";
        end:;
    }
}
```

## 3.Leetcode 217 存在重复元素（进阶版）

[原题链接](https://leetcode.cn/problems/contains-duplicate/)

给你一个整数数组 `nums` 。如果任一值在数组中出现 **至少两次** ，返回 `true` ；如果数组中每个元素互不相同，返回 `false` 。

 

**示例 1：**

**输入：**nums = [1,2,3,1]

**输出：**true

**解释：**

元素 1 在下标 0 和 3 出现。

### 1.最暴力方法：使用枚举筛查 O(n^2)

```c++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        vector<int> findnums;
        findnums.reserve(10000);
        int input=0;
        for(int i=0;i<nums.size();i++){
            auto it=std::find(findnums.begin(),findnums.end(),nums[i]);
            if(it!=findnums.end()){
                input=1;
                break;
            }else{
                findnums.push_back(nums[i]);
            }
        }
        return bool(input);
    }
};
```

分析：效率过低，每次插入一个新值都需要重新查找一遍findnums数组

优化：将findnums数组优化为unordered_set（使用哈希表来存储出现过的元素效率更高）

### 优化代码：

```C++
class Solution {
public:
    bool containsDuplicate(std::vector<int>& nums) {
        std::unordered_set<int> seen;
        for (int num : nums) {
            //编程习惯：遍历的简化写法
            if (seen.find(num) != seen.end()) {
                //如果找到了num,直接输出true
                return true;
            }
            seen.insert(num);
        }
        return false;
    }
};

```

### 知识补充：哈希表

​	使用哈希表（如 `std::unordered_set`）存储已经遇到的元素，**时间复杂度更低**的原因主要在于哈希表的查找和插入操作的平均时间复杂度是 O(1)。这是因为哈希表通过哈希函数将元素映射到一个数组中的特定位置，从而实现了快速的查找和插入。以下是详细解释：

##### 哈希表的工作原理

1. **哈希函数**：哈希表使用一个哈希函数将每个元素的值转换为一个哈希码（通常是一个整数），这个哈希码对应哈希表中的一个位置（桶）。
2. **存储位置**：元素被存储在对应的桶中，查找和插入操作都可以通过计算哈希码直接定位到相应的桶，从而避免了线性扫描。
3. **冲突处理**：当多个元素映射到同一个桶时，哈希表使用冲突处理机制（如链地址法或开放地址法）来处理这些冲突。

##### 时间复杂度分析

- **查找**：在理想情况下，哈希表的查找操作只需计算一次哈希函数并访问一个桶，时间复杂度为 O(1)。
- **插入**：插入操作也只需计算一次哈希函数并将元素放入相应的桶中，时间复杂度为 O(1)。
- **删除**：类似于查找和插入，删除操作也可以在 O(1) 时间内完成。

##### 对比线性查找

- **线性查找**：在向量或链表中查找一个元素需要遍历所有元素，时间复杂度为 O(n)。（例如vector)
- **哈希查找**：在哈希表中查找一个元素平均只需要 O(1) 时间，可以显著提高查找效率。

##### 实际应用中的考虑

虽然哈希表的平均时间复杂度是 O(1)，但在最坏情况下（例如哈希函数不均匀导致所有元素映射到同一个桶），时间复杂度可以退化到 O(n)。然而，通过选择合适的哈希函数和合理的负载因子，最坏情况发生的概率可以被大大降低。

##### 结论

使用哈希表存储已经遇到的元素，可以利用其快速查找和插入的特性，将包含重复元素的检查操作的时间复杂度从 O(n^2) 降低到 O(n)，在处理大数据集时显著提高性能。

## 4.Leetcode 219 存在重复元素II （进阶版）

给你一个整数数组 `nums` 和一个整数 `k` ，判断数组中是否存在两个 **不同的索引** `i` 和 `j` ，满足 `nums[i] == nums[j]` 且 `abs(i - j) <= k` 。如果存在，返回 `true` ；否则，返回 `false` 。

思路：此处使用unordered_set会比较麻烦，unordered_set 键值对就是自己本身，这里可以使用unordered_map类型的容器，实现两个值之间的一一映射。

（在上一题中只是判断是否存在，对元素的索引和顺序并未提出要求，故使用unordered_set 即可）

### 代码实现：使用映射

```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_map<int, int> dictionary; // 用于存储每个元素的最新索引
        int length = nums.size(); // 获取数组的长度
        for (int i = 0; i < length; i++) { // 遍历数组
            int num = nums[i]; // 当前元素
            // 如果当前元素已经存在于字典中，并且当前索引与存储的索引之差小于等于 k
            if (dictionary.count(num) && i - dictionary[num] <= k) {
                return true; // 找到符合条件的重复元素，返回 true
            }
            dictionary[num] = i; // 更新字典中的索引  nums[i]->i的映射
        }
        return false; // 遍历完数组后，未找到符合条件的重复元素，返回 false
    }
};

```

#### 相关代码解释

- 基本思路：创建dictionary的map映射，并对nums数组不断进行操作，如果找到两个相同的元素并且索引小于k，直接return true，如果没有，则储存一对新的映射（元素唯一）

- unordered_map容器中键值保持唯一性，使用 `[]` 运算符插入相同键时，新值会覆盖旧值。使用 `insert` 方法插入相同键时，插入操作会失败，原值保持不变。

- count函数：

  `std::unordered_map` 的 `count` 函数用于检查特定键是否存在于映射中。它返回一个整数值，表示具有指定键的元素数量。在 `std::unordered_map` 中，每个键是唯一的，因此 `count` 函数的返回值只能是 `0` 或 `1`。

  ```cpp
  size_t count(const Key& key) const;
  ```

  - `key`：要检查的键。
  - 返回值：如果键存在，则返回 `1`；否则返回 `0`。

  ```cpp
  #include <iostream>
  #include <unordered_map>
  
  int main() {
      std::unordered_map<int, std::string> myMap;
      myMap[1] = "one";
      myMap[2] = "two";
      myMap[3] = "three";
  
      int key = 2;
  
      // 使用 count 函数检查键是否存在
      if (myMap.count(key)) {
          std::cout << "Key " << key << " exists in the map with value: " << myMap[key] << std::endl;
      } else {
          std::cout << "Key " << key << " does not exist in the map." << std::endl;
      }
  
      return 0;
  }
  ```

  ```
  Key 2 exists in the map with value: two
  ```

#### 容器：unordered_map简介	

`unordered_map` 是 C++ 标准库中的一个关联容器，用于存储键值对。它基于哈希表实现，提供了平均常数时间复杂度的插入、删除和查找操作。

特性

1. **无序存储**：元素存储在哈希表中，因此没有特定的顺序。
2. **唯一键**：每个键在容器中是唯一的。
3. **高效操作**：插入、删除和查找操作的平均时间复杂度为 `O(1)`，最坏情况下为 `O(n)`，但这种情况很少发生。

常用操作

- **插入元素**：使用 `insert` 方法或 `[]` 运算符。
- **删除元素**：使用 `erase` 方法。
- **查找元素**：使用 `find` 方法或 `count` 方法。
- **访问元素**：使用 `[]` 运算符。

示例

```cpp
#include <iostream>
#include <unordered_map>

int main() {
    std::unordered_map<int, std::string> myMap;

    // 插入元素
    myMap[1] = "one";
    myMap[2] = "two";
    myMap.insert({3, "three"});

    // 访问元素
    std::cout << "Key 1: " << myMap[1] << std::endl;
    std::cout << "Key 2: " << myMap[2] << std::endl;

    // 查找元素
    auto it = myMap.find(3);
    //find函数用来返回一个迭代器，如果找到就返回特定键值的迭代器，如果没有找到就返回对应的.end()迭代器
    if (it != myMap.end()) {
        std::cout << "Found key 3 with value: " << it->second << std::endl;
    } else {
        std::cout << "Key 3 not found." << std::endl;
    }

    // 删除元素
    myMap.erase(2);

    // 检查元素是否存在
    if (myMap.count(2)) {
        std::cout << "Key 2 exists in the map." << std::endl;
    } else {
        std::cout << "Key 2 does not exist in the map." << std::endl;
    }

    return 0;
}
```

输出：

```
Key 1: one
Key 2: two
Found key 3 with value: three
Key 2 does not exist in the map.
```

### 思路2：滑动窗口

根据题目要求，并不是所有的题目的数据都要用一个数组进行储存再查找的过程。

思路：设置滑动窗口的双枚举i，j

```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        for(int i=0;i<nums.size();i++){
            for(int j=i+1;j<=i+k&&j<nums.size();j++){
                if(nums[i]==nums[j]){
                    return true;
                }
            }
        }
        return false;
    }
};
```

时间复杂度：O(Kn)      空间复杂度O(1)

算法优化：

​	考虑数组 nums 中的每个长度不超过 k+1 的滑动窗口，同一个滑动窗口中的任意两个下标差的绝对值不超过 k。如果存在一个滑动窗口，其中有重复元素，则**存在两个不同的下标 i 和 j 满足 nums[i]=nums[j] 且 ∣i−j∣≤k**。如果所有滑动窗口中都没有重复元素，则不存在符合要求的下标。因此，只要**遍历每个滑动窗口，判断滑动窗口中是否有重复元素即可**。

​	如果一个滑动窗口的结束下标是 i，则该滑动窗口的开始下标是 max(0,i−k)。可以使用哈希集合存储滑动窗口中的元素。从左到右遍历数组 nums，当遍历到下标 i 时，具体操作如下：

​	如果 i>k，则下标 i−k−1 处的元素被移出滑动窗口，因此将 nums[i−k−1] 从哈希集合中删除；

​	判断 nums[i] 是否在哈希集合中，如果在哈希集合中则在同一个滑动窗口中有重复元素，返回 true，如果不在哈希集合中则将其加入哈希集合。

​	当遍历结束时，如果所有滑动窗口中都没有重复元素，返回 false。

```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_set<int> s;
        //s就是滑动窗口，用一个unordered_set存储，可以减少一个for循环，降低时间复杂度
        int length = nums.size();
        for (int i = 0; i < length; i++) {
            if (i > k) {
                s.erase(nums[i - k - 1]);
            }
            //当i>k时，每次执行i++时都需要将滑动窗口的最后一个元素驱逐出窗口
            if (s.count(nums[i])) {
                return true;
            }
            s.emplace(nums[i]);
            //在每一次i++前，都会插入一个新元素，之后会执行命令count，判断新插入的元素是否已经存在在滑动窗口中，如果是，直接return true
        }
        return false;
    }
};

```

时间复杂度：O(n)

#### emplace函数

`emplace` 是 C++11 引入的一个成员函数，用于在容器中原地构造元素。对于 `unordered_map`，`emplace` 可以避免不必要的临时对象创建和复制，从而提高性能。`emplace` 方法接受键和值的构造参数，并直接在容器中构造元素。

`emplace` 的用法

`emplace` 的函数签名如下：

```cpp
template <class... Args>
std::pair<iterator, bool> emplace(Args&&... args);
```

- **参数**：`args` 是传递给元素构造函数的参数。
- **返回值**：返回一个 `std::pair`，其中第一个元素是指向插入元素的迭代器，第二个元素是一个布尔值，表示插入是否成功。

示例

以下示例展示了如何使用 `emplace` 在 `unordered_map` 中插入元素：

```cpp
#include <iostream>
#include <unordered_map>

int main() {
    std::unordered_map<int, std::string> myMap;

    // 使用 emplace 插入元素
    myMap.emplace(1, "one");
    myMap.emplace(2, "two");

    // 打印初始内容
    std::cout << "Initial map:" << std::endl;
    for (const auto& pair : myMap) {
        std::cout << "Key: " << pair.first << ", Value: " << pair.second << std::endl;
    }

    // 尝试使用 emplace 插入相同键
    auto result = myMap.emplace(1, "uno");

    if (result.second) {
        std::cout << "Emplace succeeded." << std::endl;
    } else {
        std::cout << "Emplace failed. Key 1 already exists with value: " << myMap[1] << std::endl;
    }

    // 打印更新后的内容
    std::cout << "Updated map:" << std::endl;
    for (const auto& pair : myMap) {
        std::cout << "Key: " << pair.first << ", Value: " << pair.second << std::endl;
    }

    return 0;
}
```

输出：

```
Initial map:
Key: 1, Value: one
Key: 2, Value: two
Emplace failed. Key 1 already exists with value: one
Updated map:
Key: 1, Value: one
Key: 2, Value: two
```

`emplace` 与 `insert` 的区别

- **`insert`**：需要先构造一个临时对象，然后再插入到容器中，可能会有额外的复制或移动操作。
- **`emplace`**：直接在容器中构造对象，避免了不必要的临时对象创建和复制操作，提高了性能。

总结

- `emplace` 方法用于在 `unordered_map` 中原地构造元素，避免不必要的临时对象创建和复制操作。
- 如果插入的键已经存在，`emplace` 操作将失败，原值保持不变。







# 程设错题 15-20 20241027-20241031

## 1.Leetcode 283 移动零

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**请注意** ，必须在不复制数组的情况下原地对数组进行操作。

**示例 1:**

```
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**示例 2:**

```
输入: nums = [0]
输出: [0]
```

**提示**:

- `1 <= nums.length <= 104`
- `-231 <= nums[i] <= 231 - 1`

### 解法①

使用vector数组中的push_back和erase成员函数对数组进行遍历，得到移动完成的数组

```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int counter=0;
        for(auto iter=nums.begin();counter<nums.size();counter++){
            if(*(iter)==0){
                nums.erase(iter,iter+1);
                nums.push_back(0);
                //如果发现0，对数组进行操作，将0删除，并添加至末尾
            }else{
                iter++;
                //如果发现的不是0，不进行操作，迭代器后移一位
            }
        }
        for(auto num:nums){
            cout<<num;
            //遍历数组输出结果
        }
        
    }
};
```

时间复杂度：O(n^2)(erase操作的时间复杂度是O(n)，再加上外层的循环)

### 解法②

解法①的低效之处：在于erase的清除过程太繁琐

改进：操作所有非零数值，使其按顺序挪到数组的开头，覆盖开头的值，并将结尾的所有剩余值修改成0。

```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size();
        int nonZeroIndex = 0;

        // 将所有非零元素移动到数组的前面
        for (int i = 0; i < n; ++i) {
            if (nums[i] != 0) {
                nums[nonZeroIndex++] = nums[i];
            }
        }

        // 将剩余的位置填充为零
        for (int i = nonZeroIndex; i < n; ++i) {
            nums[i] = 0;
        }

        // 打印数组
        for (auto num : nums) {
            cout << num << " ";
        }
        cout << endl;
    }
};
```

时间复杂度：O(n)

### 解法③ 双指针并行处理

```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size(), left = 0, right = 0;
        while (right < n) {
            if (nums[right]) {
                swap(nums[left], nums[right]);
                left++;
                //如果nums[right]不是0，那么和左指针发生交换，右指针是试探指针，左指针代表已经检查过是非零值的元素，并将左指针右移一位
                //最坏的情况:没有0，则左指针和右指针完全同步
            }
            right++;
        }
    }
};

```

时间复杂度：O(n)

## 2.Leetcode 290 单词映射

给定一种规律 `pattern` 和一个字符串 `s` ，判断 `s` 是否遵循相同的规律。

这里的 **遵循** 指完全匹配，例如， `pattern` 里的每个字母和字符串 `s` 中的每个非空单词之间存在着双向连接的对应规律。

**示例1:**

```
输入: pattern = "abba", s = "dog cat cat dog"
输出: true
```

**示例 2:**

```
输入:pattern = "abba", s = "dog cat cat fish"
输出: false
```

**示例 3:**

```
输入: pattern = "aaaa", s = "dog cat cat dog"
输出: false
```

 代码解法

思路：

- 如何获得子字符串作为单词？

  - 使用substr()

  - 使用双指针

  - `substr` 是 C++ 中 `std::string` 类的一个成员函数，用于从字符串中提取子字符串。它有两个参数：

    1. **起始位置（pos）**：要提取的子字符串的起始索引。
    2. **长度（len）**：要提取的子字符串的长度（可选）。如果不指定，则提取到字符串的末尾。

    语法

    ```cpp
    std::string substr(size_t pos = 0, size_t len = npos) const;
    ```

    - `pos`：开始提取的位置。
    - `len`：要提取的字符数。
    - `npos`：一个特殊值，表示直到字符串的末尾。

    示例

    ```cpp
    #include <iostream>
    #include <string>
    
    int main() {
        std::string text = "Hello, World!";
        
        // 提取从位置 7 开始的子字符串，长度为 5
        std::string sub1 = text.substr(7, 5);
        std::cout << sub1 << std::endl;  // 输出 "World"
        
        // 提取从位置 7 开始的子字符串，直到字符串末尾
        std::string sub2 = text.substr(7);
        std::cout << sub2 << std::endl;  // 输出 "World!"
    
        return 0;
    }
    ```

    注意事项

    - 如果 `pos` 超出字符串长度，会抛出 `std::out_of_range` 异常。
    - 如果 `len` 超出可用长度，则提取到字符串的末尾。

    `substr` 是处理字符串时非常有用的工具，尤其是在需要提取特定部分的场景中。

- 如何构建一一映射的关系

  - 使用两个unordered_map

- 如何处理两个数量不相等的情况

  - 使用计数器

```C++
class Solution {
public:
    bool wordPattern(string pattern, string str) {
        unordered_map<string, char> str2ch;
        unordered_map<char, string> ch2str;
        int m = str.length();
        int i = 0;
        for (auto ch : pattern) {
            if (i >= m) {
                return false;
                //如果 i 超过或等于 str 的长度，说明 str 中的单词数量不足以匹配 pattern，返回 false(此时ch还在遍历Pattern，但是i已经超过str的长度了，说明str不够长)
            }
            int j = i;
            //经典的双指针解法，截取一个子字符串
            while (j < m && str[j] != ' ') j++;
            //截取一个单词
            const string &tmp = str.substr(i, j - i);
            //此处已经跳出循环，故包含了' '的情况，单词长度就是j-i
            if (str2ch.count(tmp) && str2ch[tmp] != ch) {
                //如果map映射中已经有了这个单词的映射，并且这个单词的映射并不等于现在遍历得到的ch
                return false;
            }
            if (ch2str.count(ch) && ch2str[ch] != tmp) {
                //反过来，如果map映射已经有了字符的映射并且这个字符的映射并不等于现在得到的单词
                return false;
            }
            //以上两个条件都为false，故在两个映射中填充进新的值。
            str2ch[tmp] = ch;
            ch2str[ch] = tmp;
            i = j + 1;
            //更新双指针ij的位置
        }
        return i >= m;
        //如果i<m,说明还有单词未被输出，说明Pattern不够长，输出false
    }
};

```

## 3. Leetcode 350 两数组交集

给你两个整数数组 `nums1` 和 `nums2` ，请你以数组形式返回两数组的交集。返回结果中每个元素出现的次数，应与元素在两个数组中都出现的次数一致（如果出现次数不一致，则考虑取较小值）。可以不考虑输出结果的顺序。

### 解法1   最基本的思路

```C++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        int list1[1001]={0};
        int list2[1001]={0};
        vector<int> succedd;
        for(auto num1:nums1){
            list1[num1]++;
        }
        for(auto num2:nums2){
            list2[num2]++;
        }
        for(int i=0;i<1001;i++){
            if(list1[i]&&list2[i]){
                for(int j=0;j<min(list1[i],list2[i]);j++){
                    succedd.push_back(i);
                }
            }
        }
        return succedd;
    }
};
```



### 解法2 优化：使用哈希表存储值

```C++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        if (nums1.size() > nums2.size()) {
            return intersect(nums2, nums1);
        }
        //为了减少空间复杂度，优先遍历元素数较少的数组
        unordered_map <int, int> m;
        for (int num : nums1) {
            ++m[num];
        }
        //遍历nums1
        vector<int> intersection;
        for (int num : nums2) {
            if (m.count(num)) {
                intersection.push_back(num);
                //如果出现，则插入到目标序列中
                --m[num];
                //相当于用掉了num1中的一个数，故要减1
                if (m[num] == 0) {
                    m.erase(num);
                }
                //踢出num出s，代表nums1中对应的数已经被用完了
            }
        }
        return intersection;
    }
};


```

### 解法3  排序+双指针遍历

**对于数组问题，可以先进行排序再进行操作！**

```C++
class Solution {
public:
    vector<int> intersect(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());
        int length1 = nums1.size(), length2 = nums2.size();
        vector<int> intersection;
        int index1 = 0, index2 = 0;
        while (index1 < length1 && index2 < length2) {
            if (nums1[index1] < nums2[index2]) {
                index1++;
            } else if (nums1[index1] > nums2[index2]) {
                index2++;
            } else {
                //nums1[]==nums2[]
                //同时向前前进一位
                intersection.push_back(nums1[index1]);
                index1++;
                index2++;
            }
        }
        return intersection;
    }
};

```

## 4. Leetcode 438 字符串的字母异位词

给定两个字符串 `s` 和 `p`，找到 `s` 中所有 `p` 的 **异位词** 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

**示例 1:**

```
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

 **示例 2:**

```
输入: s = "abab", p = "ab"
输出: [0,1,2]
解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

###  解法1 使用基本思路

从i=0开始，遍历s数组，切割得到子串，然后判定s的子串和p是否为异位串。

**本质上是优化的滑动窗口**

```C++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> ans;
        if (s.length() < p.length()) {
        } else {
            for (int i = 0; i + p.length() - 1 < s.length(); i++) {
                string s2 = s.substr(i, p.length());
                if (judgeyiwei(s2, p)) {
                    ans.push_back(i);
                }
            }
        }
        return ans;
    }
    bool judgeyiwei(string s1, string s2) {
        int numlist[26] = {0};
        for (int i = 0; i < s1.length(); i++) {
            numlist[s1[i] - 'a']++;
            numlist[s2[i] - 'a']--;
        }
        for (int i = 0; i < 26; i++) {
            if (numlist[i]) {
                return false;
            }
        }
        return true;
    }
};
```

### 解法2 不定长滑动窗口优化

基本原理：枚举子串 s ′的右端点，如果发现 s ′其中一种字母的出现次数大于 p 的这种字母的出现次数，则右移 s ′的左端点。如果发现 s ′的长度等于 p 的长度，则说明 s ′的每种字母的出现次数，和 p 的每种字母的出现次数都相同，那么 s ′是 p 的异位词。

**有点类似于双指针**

```C++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        vector<int> ans;
        int cnt[26]{0}; // 统计 p 的每种字母的出现次数
        for (char c : p) {
            cnt[c - 'a']++;
        }
        //接下来对s字符串进行遍历操作(遍历右端点作为外层循环，在内部用while循环控制左端点)
        int left = 0;
        for (int right = 0; right < s.size(); right++) {
            int c = s[right] - 'a';
            cnt[c]--; // 右端点字母进入窗口，相当于把p中减掉一个对应的字符
            while (cnt[c] < 0) { // c对应的字符(ASCII)太多了
                cnt[s[left] - 'a']++; // 左端点字母离开窗口
                left++; 
            }
            if (right - left + 1 == p.length()) { // s' 和 p 的每种字母的出现次数都相同
                ans.push_back(left); // s' 左端点下标加入答案
            }
        }
        return ans;
    }
};

```

## 5.Leetcode 443 字符串压缩问题

给你一个字符数组 `chars` ，请使用下述算法压缩：

从一个空字符串 `s` 开始。对于 `chars` 中的每组 **连续重复字符** ：

- 如果这一组长度为 `1` ，则将字符追加到 `s` 中。
- 否则，需要向 `s` 追加字符，后跟这一组的长度。

压缩后得到的字符串 `s` **不应该直接返回** ，需要转储到字符数组 `chars` 中。需要注意的是，如果组长度为 `10` 或 `10` 以上，则在 `chars` 数组中会被拆分为多个字符。

请在 **修改完输入数组后** ，返回该数组的新长度。

你必须设计并实现一个只使用常量额外空间的算法来解决此问题。

**必须在原来的数组上进行修改！**

### 解法：双指针

为了实现原地压缩，我们可以使用双指针分别标志我们在字符串中读和写的位置。每次当读指针 read 移动到某一段连续相同子串的最右侧，我们就在写指针 write 处依次写入该子串对应的字符和子串长度即可。

在实际代码中，当读指针 read 位于字符串的末尾，或读指针 read 指向的字符不同于下一个字符时，我们就认为**读指针 read 位于某一段连续相同子串的最右侧**。该子串对应的字符即为读指针 read 指向的字符串。我们使用变量 left 记录该子串的最左侧的位置，这样子串长度即为 read−left+1。

特别地，为了达到 O(1) 空间复杂度，我们需要自行实现将数字转化为字符串写入到原字符串的功能。这里我们采用短除法将子串长度倒序写入原字符串中，然后再将其反转即可。

```C++
class Solution {
public:
    int compress(vector<char>& chars) {
        int n = chars.size();
        int write = 0, left = 0;
        for (int read = 0; read < n; read++) {
            if (read == n - 1 || chars[read] != chars[read + 1]) {
                //由于写的速度肯定没有读的快，故不用担心write会覆盖未被读取的初始值
                chars[write++] = chars[read];
                int num = read - left + 1;
                if (num > 1) {
                    int anchor = write;
                    while (num > 0) {
                        chars[write++] = num % 10 + '0';
                        num /= 10;
                    }
                    reverse(&chars[anchor], &chars[write]);
                    //实现输入一个数字倒序插入char数组中（也可以使用stringstream）
                }
                left = read + 1;
                //后面还会执行read++，本质上就是更新read和left的位置使其对齐
            }
        }
        return write;
    }
};


```

