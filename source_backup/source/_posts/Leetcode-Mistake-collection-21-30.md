---
title: Leetcode-Mistake-collection-21-30
date: 2024-12-10 13:07:14
index_img: /img/cover/Leetcode.jpg
excerpt: Leetcode-Mistake-Collection-Episode-3, focusing on Greedy Algorithm and dynamical programming.
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

# Leetcode-Mistake-collection-21-30

# 程设错题 21-24 2024-11-1 2024-11-4

## 1. Leetcode 453 最小操作次数使数组元素相等

**不要所有方法都使用最原始的编程方法解决！**

给你一个长度为 `n` 的整数数组，每次操作将会使 `n - 1` 个元素增加 `1` 。返回让数组所有元素相等的最小操作次数。

**示例 1：**

```
输入：nums = [1,2,3]
输出：3
解释：
只需要3次操作（注意每次操作会增加两个元素的值）：
[1,2,3]  =>  [2,3,3]  =>  [3,4,3]  =>  [4,4,4]
```

### 解法1 最基本解法（计算+枚举）

​	**时间复杂度过高**（最坏时间复杂度：O(n^2)）

```C++
class Solution {
public:
    int minMoves(vector<int>& nums) {
        if(nums.size()==1){
            return 0;
        }
        int max=nums[0];
        long long sum=0;
        for(auto num:nums){
            sum+=num;
            if(num>max){
                max=num;
            }
        }
        long long diff=nums.size()*max-sum;
        while(diff%(nums.size()-1)){
            diff+=(nums.size());
        }
        bool flag=0;
        long long standard=(long(diff)+sum)/nums.size();
        while(!flag){
            for(auto num:nums){
                if(standard-num>(diff/(nums.size()-1))){
                    flag=0;
                    standard+=nums.size()-1;
                    diff+=nums.size()*(nums.size()-1);
                    break;
                }
                flag=1;
            }
        }
        return diff/(nums.size()-1);
    }
};
```

### 解法2 巧妙的转化

​	因为只需要找出让数组所有元素相等的最小操作次数，所以我们不需要考虑数组中各个元素的绝对大小，即不需要真正算出数组中所有元素相等时的元素值，只需要考虑数组中元素相对大小的变化即可。

​	因此，每次操作既可以理解为使 n−1 个元素增加 1，也可以理解使 1 个元素减少 1。显然，后者更利于我们的计算。

​	于是，要计算让数组中所有元素相等的操作数，我们只需要计算将数组中所有元素都减少到数组中元素最小值所需的操作数。

**这个方法没有次数的限制，所以不用通过枚举找到可行解！**

```c++
class Solution {
public:
    int minMoves(vector<int>& nums) {
        int minNum = *min_element(nums.begin(),nums.end());
        //找到数组中的最小值
        int res = 0;
        for (int num : nums) {
            res += num - minNum;
        }
        //通过累加得到一共需要操作的次数
        return res;
    }
};
```

## 2. Leetcode 448 找到数组中消失的数字

给你一个含 `n` 个整数的数组 `nums` ，其中 `nums[i]` 在区间 `[1, n]` 内。请你找出所有在 `[1, n]` 范围内但没有出现在 `nums` 中的数字，并以数组的形式返回结果。

### 解法1 使用动态数组（new&delete）

```C++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        vector <int> successful;
        int n=nums.size();
        bool *list=new bool[n];
        for(int i=0;i<n;i++){
            list[i]=false;
        }
        for(auto num:nums){
            list[num-1]=true;
        }
        for(int i=0;i<n;i++){
            if(!list[i]){
                successful.push_back(i+1);
            }
        }
        delete[] list;
        return successful;
        
    }
};
```

### 解法2 原地哈希表

**不开辟新的内存空间，在原来的数组上进行操作**

我们可以用一个哈希表记录数组 nums 中的数字，由于数字范围均在 [1,n] 中，记录数字后我们再利用哈希表检查 [1,n] 中的每一个数是否出现，从而找到缺失的数字。

由于数字范围均在 [1,n] 中，我们也可以用一个长度为 n 的数组来代替哈希表。这一做法的空间复杂度是 O(n) 的。我们的目标是优化空间复杂度到 O(1)。

注意到 nums 的长度恰好也为 n，能否让 nums 充当哈希表呢？

由于 nums 的数字范围均在 [1,n] 中，我们可以**利用这一范围之外的数字**，来表达「是否存在」的含义。

具体来说，遍历 nums，每遇到一个数 x，就让 nums[x−1] 增加 n。由于 nums 中所有数均在 [1,n] 中，增加以后，这些数必然大于 n。最后我们遍历 nums，若 nums[i] 未大于 n，就说明没有遇到过数 i+1。这样我们就找到了缺失的数字。

注意，**当我们遍历到某个位置时，其中的数可能已经被增加过，因此需要对 n 取模**来还原出它本来的值。

**取模&加倍数（进制）**可以让一个数通过不同的运算储存不同的信息，例如32以10为进制可以储存3,2两个信息

```C++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int n = nums.size();
        for (auto& num : nums) {
            int x = (num - 1) % n;
            //-1 problem，注意数组下标从0开始
            //通过%取余运算得到原来的值
            nums[x] += n;
        }
        vector<int> ret;
        for (int i = 0; i < n; i++) {
            if (nums[i] <= n) {
                //证明这个值未出现过
                ret.push_back(i + 1);
            }
        }
        return ret;
    }
};

```

## 3. Leetcode 455 贪心算法分配饼干

### 补充：贪心算法

**保证局部最优解=全局最优解！**

#### 1. 最优子结构性质

**定义**：一个问题具有最优子结构性质，意味着问题的最优解可以通过其子问题的最优解来构建。这是动态规划和贪婪算法的共同特征。

**作用**：如果一个问题具有最优子结构性质，贪婪算法可以通过解决每个子问题的最优解来逐步构建全局最优解。例如，在最小生成树问题中，局部最优的边选择能够合并成全局最优的生成树。

#### 2. 无后效性

**定义**：无后效性是指当前选择不会影响未来选择的可行性或最优性。这意味着每一步做出的选择不会限制后续步骤中其他选择的可能性。

**作用**：无后效性确保了贪婪算法在每一步做出局部最优选择时，不会对后续步骤产生负面影响。例如，在活动选择问题中，选择结束时间最早的活动不会影响后续活动的选择空间。

#### 3. 贪婪选择性质的应用场景

一些典型的应用场景包括：

- **活动选择问题**：选择一组互不重叠的活动，贪婪选择性质确保选择最早结束的活动是最优的。
- **最小生成树问题**：通过选择权重最小且不形成环的边来构建最小生成树。
- **单源最短路径问题（无负权边）**：在Dijkstra算法中，每次选择当前未访问顶点中距离最小的顶点。

#### 4. 不适用贪婪算法的问题

并不是所有问题都适合用贪婪算法来解决。对于不具备贪婪选择性质的问题，贪婪算法可能无法找到全局最优解。例如：

- **旅行商问题（TSP）**：贪婪算法可能无法找到最短路径，因为局部最优选择可能导致全局次优解。
- **背包问题**：在0-1背包问题中，贪婪算法可能无法找到最优解，因为选择价值密度最高的物品可能导致无法装入其他更有价值的组合。

#### 5. 识别适用性

在应用贪婪算法之前，识别问题是否具备贪婪选择性质和最优子结构是关键。通常需要通过理论分析或构造反例来验证贪婪策略的有效性。

总结来说，贪婪算法适用于那些具有最优子结构和无后效性的问题。在这些问题中，贪婪选择性质确保每一步的局部最优选择最终能组合成全局最优解。对于不具备这些性质的问题，可能需要其他算法（如动态规划或回溯）来找到全局最优解。

### 题目：

假设你是一位很棒的家长，想要给你的孩子们一些小饼干。但是，每个孩子最多只能给一块饼干。

对每个孩子 `i`，都有一个胃口值 `g[i]`，这是能让孩子们满足胃口的饼干的最小尺寸；并且每块饼干 `j`，都有一个尺寸 `s[j]` 。如果 `s[j] >= g[i]`，我们可以将这个饼干 `j` 分配给孩子 `i` ，这个孩子会得到满足。你的目标是满足尽可能多的孩子，并输出这个最大数值。

### 解法1:排序后遍历（繁琐做法）

时间复杂度：*O*(*m*log*m*+*n*log*n*+*m*⋅*n*)

​	mlogm和nlogn是排序的时间复杂度，mn是最后遍历的时间复杂度

```C++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        int count=0;
        int select=0;
        for(int i=0;i<g.size()&&select<s.size();i++){
            for(int j=select;j<s.size();j++){
                if(s[j]>=g[i]){
                    count++;
                    j++;
                    select=j;
                    break;
                }
            }
            //其实这里通过select不断提升j的枚举起点有点类似于双指针策略，但是代码的冗余之处在于如果出现遍历完还没有找到的情况，应该直接跳出外循环（我们已经排序过数组了）
        }
        return count;
    }
};
```

解法1的小修改:

```C++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(),g.end());
        sort(s.begin(),s.end());
        int count=0;
        int select=0;
        bool flag=false;
        for(int i=0;i<g.size()&&select<s.size();i++){
            for(int j=select;j<s.size();j++){
                if(s[j]>=g[i]){
                    flag=true;
                    count++;
                    j++;
                    select=j;
                    break;
                }
            }
            if(!flag){
                return count;
            }
        }
        return count;
        
    }
};
```

(这样就不会超时了，但是代码还有优化的空间)

- 这里其实不需要写两层for循环控制两个指针！

### 解法2：优化使用双指针

```C++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());
        int count = 0;
        int i = 0, j = 0;
        //define two pointers
        while (i < g.size() && j < s.size()) {
            if (s[j] >= g[i]) {
                count++;
                i++;
                //if allocate successfully,then the both pointers get forward
            }
            j++;
            //whenever the allocate, the biscuit pointer gets forward
        }
        return count;
    }
};

```

时间复杂度：O(mlogm)+O(nlogn)+O(m+n)=O(mlogm+nlogn)

### 补充：双指针

双指针是一种常用的算法技巧，尤其在处理数组或链表等线性数据结构时非常有效。它通过使用两个指针来遍历数据结构，能够在某些情况下优化时间复杂度或简化逻辑。以下是双指针技巧的一些常见应用场景和简单介绍：

### 1. 快慢指针

- **应用场景**：检测链表中的环、找到链表的中间节点。
- **方法**：
  - 使用两个指针，一个快指针每次移动两步，一个慢指针每次移动一步。
  - 如果快指针和慢指针相遇，说明存在环。
  - 找中间节点时，当快指针到达链表末尾时，慢指针正好在中间。

### 2. 左右指针

- **应用场景**：解决排序数组中的问题，如二分查找、三数之和。
- **方法**：
  - 初始化两个指针，分别指向数组的两端。
  - 根据问题的要求，向中间移动指针。
  - 常用于查找满足某种条件的对或子序列。
  - 也可以实现动态的查找。

### 3. 滑动窗口

- **应用场景**：解决子数组问题，如最长无重复子串、最小覆盖子串。
- **方法**：
  - 使用两个指针定义一个窗口，初始时窗口为空。
  - 移动右指针扩展窗口，移动左指针缩小窗口，直到满足条件。
  - 在每次窗口变化时更新结果。

### 示例：使用双指针解决有序数组中的两数之和

假设我们有一个有序数组，想找到两个数，使它们的和等于目标值。

```cpp
vector<int> twoSum(vector<int>& numbers, int target) {
    int left = 0, right = numbers.size() - 1;
    while (left < right) {
        int sum = numbers[left] + numbers[right];
        if (sum == target) {
            return {left + 1, right + 1}; // 返回索引从1开始
        } else if (sum < target) {
            left++; // 增加左指针以增加总和
        } else {
            right--; // 减少右指针以减少总和
        }
    }
    return {}; // 如果没有找到，返回空
}
```

### 总结

双指针技巧通过合理移动两个指针，可以有效地减少遍历次数或简化算法逻辑。选择合适的双指针策略（如快慢指针、左右指针、滑动窗口）可以帮助解决许多复杂的算法问题。

## 4. ACwing 633 两数平方和判断

给定一个非负整数 `c` ，你要判断是否存在两个整数 `a` 和 `b`，使得 `a^2 + b^2 = c` 。

**示例 1：**

```
输入：c = 5
输出：true
解释：1 * 1 + 2 * 2 = 5
```

**示例 2：**

```
输入：c = 3
输出：false
```

**提示：**

- `0 <= c <= 2^31 - 1`

最暴力的解法：双循环暴力枚举（复杂度：O(n^2))

优化方法：使用sqrt函数省去一层循环

```C++
class Solution {
public:
    bool judgeSquareSum(int c) {
        double b;
        for(long long  i=0;(i)*i<=c;i++){
            b=sqrt(c-i*i);{
                if(b==int(b)){
                    return true;
                }
            }
        }
        return false;
    }
};
```

#### 补充方法：使用双指针枚举

![two pointers](1.png)

```C++
class Solution {
public:
    bool judgeSquareSum(int c) {
        long left = 0;
        long right = (int)sqrt(c);
        while (left <= right) {
            long sum = left * left + right * right;
            if (sum == c) {
                return true;
            } else if (sum > c) {
                right--;
            } else {
                left++;
            }
        }
        return false;
    }
};

```





# 程序设计错题  25-30 20241105

## 1. Leetcode 877 先手必胜策略判断

Alice 和 Bob 用几堆石子在做游戏一共有偶数堆石子，**排成一行**；每堆都有 **正** 整数颗石子，数目为 `piles[i]` 。

游戏以谁手中的石子最多来决出胜负。石子的 **总数** 是 **奇数** ，所以没有平局。

Alice 和 Bob 轮流进行，**Alice 先开始** 。 每回合，玩家从行的 **开始** 或 **结束** 处取走整堆石头。 这种情况一直持续到没有更多的石子堆为止，此时手中 **石子最多** 的玩家 **获胜** 。

假设 Alice 和 Bob 都发挥出最佳水平，当 Alice 赢得比赛时返回 `true` ，当 Bob 赢得比赛时返回 `false` 。

**示例 1：**

```
输入：piles = [5,3,4,5]
输出：true
解释：
Alice 先开始，只能拿前 5 颗或后 5 颗石子 。
假设他取了前 5 颗，这一行就变成了 [3,4,5] 。
如果 Bob 拿走前 3 颗，那么剩下的是 [4,5]，Alice 拿走后 5 颗赢得 10 分。
如果 Bob 拿走后 5 颗，那么剩下的是 [3,4]，Alice 拿走后 4 颗赢得 9 分。
这表明，取前 5 颗石子对 Alice 来说是一个胜利的举动，所以返回 true 。
```

**示例 2：**

```
输入：piles = [3,7,2,3]
输出：true
```

**提示：**

- `2 <= piles.length <= 500`
- `piles.length` 是 **偶数**
- `1 <= piles[i] <= 500`
- `sum(piles[i])` 是 **奇数**

**分析**：此题贪婪算法并不适应，因为局部最优并不是全局最优

#### 为什么不能用贪婪算法？

1. **局部最优不等于全局最优**：在这种零和博弈中，贪婪策略只能保证每次选择当前最佳（即最多的石子堆），但这并不一定能得到最终的胜利。因为后续的选择会影响到剩余石子的选择，而不只是当前的选择。例如，某个玩家如果总是选择当前最多的石子堆，可能会在后续失去更多的机会，最终导致输掉游戏。
2. **博弈的前瞻性**：每次玩家的选择不仅仅影响当前局面，还会影响到接下来的选择。理想的策略需要考虑未来的回合，预测对方的反应，并选择一个能最终获得胜利的方案。贪婪算法通常缺乏这种全局视野。
3. **博弈的相互作用**：这道题的核心在于两方玩家都在做决策，而每个决策都会影响到后续的局面。需要通过动态规划来考虑在所有可能的局面下，哪种选择能够获得最终最优的结果。

### 解法 ：区间动态规划

为了求解这个问题，我们可以定义一个动态规划表 `dp[i][j]` 来表示在石子堆的区间 `[i, j]` 中，**当前玩家**（注意有可能是Bob！）与对手玩家的所得到石子之差的最大值。

#### 状态转移方程

假设当前玩家面对的石子堆是 `[i, j]`，他可以选择：

- 从前端取走 `piles[i]`，剩下的区间是 `[i+1, j]`，这个时候，剩下的区间 `[i+1, j]` 会是对方的回合。
- 从后端取走 `piles[j]`，剩下的区间是 `[i, j-1]`，这个时候，剩下的区间 `[i, j-1]` 会是对方的回合。

对于每种选择，当前玩家的得分是**他取走的石子的数量加上对方在剩余区间中的最差得分**。为了确保当前玩家的策略最优，我们需要选取对方最差的选择（即，最小化对方得到的分数）。

因此，状态转移方程为：

- `dp[i][j] = max(piles[i] + (sum(i+1, j) - dp[i+1][j]), piles[j] + (sum(i, j-1) - dp[i][j-1]))`
- 这里是全局最优解！

其中，`sum(i, j)` 是从 `i` 到 `j` 的石子堆总数。

#### 动态规划实现

为了避免重复计算区间和，我们可以预先计算所有区间的石子总和，并使用这些预计算的和来加速我们的 DP 计算。

```C++
class Solution {
public:
    bool stoneGame(vector<int>& piles) {
        int length = piles.size();
        auto dp = vector<vector<int>>(length, vector<int>(length));
        //定义二维数组
        for (int i = 0; i < length; i++) {
            dp[i][i] = piles[i];
        }
        //特殊情况：游戏进行到了最后一轮
        for (int i = length - 2; i >= 0; i--) {
            for (int j = i + 1; j < length; j++) {
                dp[i][j] = max(piles[i] - dp[i + 1][j], piles[j] - dp[i][j - 1]);
            }
        }
        //特殊的检索方法。类似于一个上三角矩阵并且确定了矩阵主对角线的元素的值
        //这样可以保证每个元素在递归的时候都有值！
        return dp[0][length - 1] > 0;
        //dp[0][length - 1]代表能拿到的最优解
        //在这里使用了归一化的思路，这样只要判断是否>0就行了
    }
};

```

使用不同的动态规划数组：

​	dp代表当前玩家在当前状态下，能够得到的最优解

```C++
class Solution {
public:
    bool stoneGame(vector<int>& piles) {
        
        int length=piles.size();
        auto sumcount=vector<vector<int>>(length,vector<int>(length));
        int sum=0;
        for(int i=0;i<length;i++){
            for(int j=0;j<length;j++){
                if(j<i){
                    sumcount[i][j]=0;
                }else{
                    for(int k=i;k<=j;k++){
                        sum+=piles[k];
                    }
                    sumcount[i][j]=sum;
                    sum=0;
                }
            }
        }
        auto dp = vector<vector<int>>(length,vector<int>(length));
        for(int i=0;i<length;i++){
            dp[i][i]=0;
        }
        for(int i=length-2;i>=0;i--){
            for(int j=i+1;j<length;j++){
                dp[i][j]=max(piles[i]+sumcount[i+1][j]-dp[i+1][j],piles[j]+sumcount[i][j-1]-dp[i][j-1]);
            }
        }
        if(2*dp[0][length-1]>sumcount[0][length-1]){
            return true;
        }else{
            return false;
        }
    }
};
```

## 2. Leetcode 942 DI字符串序列匹配生成

由范围 `[0,n]` 内所有整数组成的 `n + 1` 个整数的排列序列可以表示为长度为 `n` 的字符串 `s` ，其中:

- 如果 `perm[i] < perm[i + 1]` ，那么 `s[i] == 'I'` 
- 如果 `perm[i] > perm[i + 1]` ，那么 `s[i] == 'D'` 

给定一个字符串 `s` ，重构排列 `perm` 并返回它。如果有多个有效排列perm，则返回其中 **任何一个** 。

**示例 1：**

```
输入：s = "IDID"
输出：[0,4,1,3,2]
```

**示例 2：**

```
输入：s = "III"
输出：[0,1,2,3]
```

**示例 3：**

```
输入：s = "DDI"
输出：[3,2,0,1]
```

### 非常经典的贪心算法！

考虑 perm[0] 的值，根据题意：

如果 s[0]=‘I’，那么令 perm[0]=0，则无论 perm[1] 为何值都满足 perm[0]<perm[1]；
如果 s[0]=‘D’，那么令 perm[0]=n，则无论 perm[1] 为何值都满足 perm[0]>perm[1]；
确定好 perm[0] 后，剩余的 n−1 个字符和 n 个待确定的数就变成了一个和原问题相同，但规模为 n−1 的问题。因此我们可以继续按照上述方法确定 perm[1]：如果 s[1]=‘I’，那么令 perm[1] 为剩余数字中的最小数；如果 s[1]=‘D’，那么令 perm[1] 为剩余数字中的最大数。如此循环直至剩下一个数，填入 perm[n] 中。

如何实现该贪心算法？

### 解法1：使用set容器自动削头去尾

```C++
class Solution {
public:
    vector<int> diStringMatch(string s) {
        vector <int> ans;
        int n=s.length();
        set <int> allthenum;
        for(int i=0;i<=n;i++){
            allthenum.insert(i);
        }
        for(int i=0;i<n;i++){
            if(s[i]=='I'){
                ans.push_back(*(allthenum.begin()));
                allthenum.erase(allthenum.begin());

            }else{
                ans.push_back((*prev(allthenum.end())));
                allthenum.erase(prev(allthenum.end()));
                
            }
        }
        ans.push_back(*(allthenum.begin()));
        return ans;}
};
```

**set容器的底层是红黑树，可以实现自动元素的排序，但不支持元素的索引，要使用迭代器**

`std::set`的迭代器是双向迭代器（bidirectional iterator），而不是随机访问迭代器（random access iterator），因此不支持减法操作。

如果你想获取`std::set`中最后一个元素的值，可以使用如下方法：

使用`std::prev`

`std::prev`是一个标准库函数，用于获取给定迭代器的前一个位置。你可以使用它来获取`set`的最后一个元素：

```cpp
#include <iostream>
#include <set>
#include <iterator> // for std::prev

int main() {
    std::set<int> allthenum = {1, 2, 3, 4, 5};

    // 获取 set 的最后一个元素
    if (!allthenum.empty()) {
        int lastElement = *std::prev(allthenum.end());
        std::cout << "Last element: " << lastElement << std::endl;
    }

    return 0;
}
```

- `allthenum.end()`返回的是一个指向`set`末尾的迭代器（即在最后一个元素之后的位置）。
- `std::prev(allthenum.end())`返回的是指向最后一个元素的迭代器。
- 使用`*`解引用这个迭代器来获取最后一个元素的值。

这种方法是标准的方式来访问`std::set`的最后一个元素。确保在访问元素之前，检查集合是否为空，以避免解引用无效迭代器。

### 解法2：优化解法：这里斩头或者去尾并不会改变剩下元素的排列顺序，所以可以使用vector数组（略）

此处也可以选择不开新的数组，直接使用双指针完成

```C++
class Solution {
public:
    vector<int> diStringMatch(string s) {
        int n = s.length(), lo = 0, hi = n;
        vector<int> perm(n + 1);
        for (int i = 0; i < n; ++i) {
            perm[i] = (s[i] == 'I' ? lo++ : hi--);
        }
        perm[n] = lo; // 最后剩下一个数，此时 lo == hi
        return perm;
    }
};

```

## 3. Leetcode 11 盛水容器

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：**你不能倾斜容器。

**示例 1：**

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

最暴力解法：双枚举（超时）

```C++
class Solution {
public:
    int maxArea(vector<int>& height) {
        long max=0;
        long ans;
        for(int i=0;i<height.size()-1;i++){
            for(int j=i+1;j<height.size();j++){
                ans=(j-i)*min(height[i],height[j]);
                if(ans>max){
                    max=ans;
                }
            }
        }
        return max;
    }
};
```

#### 解法优化：对枚举的剪枝操作

剪枝算法是一种在搜索算法中用于减少计算量和提高效率的技术。它通过在搜索过程中剪去不必要的分支，从而避免遍历整个搜索空间。剪枝算法在许多领域中都有应用，尤其是在解决组合优化问题和博弈树搜索（如棋类游戏）时非常有效。以下是剪枝算法的一些关键概念和常见应用：

##### 关键概念

1. **搜索空间**：这是所有可能解的集合。搜索算法的目标是找到满足某些条件的解。

2. **剪枝条件**：在搜索过程中，算法会评估当前路径或分支是否有可能产生更优解。如果确定某个分支不可能产生更优解，则可以安全地剪去该分支。

3. **有效性**：剪枝的有效性在于它能显著减少需要评估的节点数量，从而加快搜索速度。

##### 常见类型

1. **Alpha-Beta 剪枝**：
   - 这是博弈树搜索中常用的剪枝算法，特别是在两人零和游戏中（如国际象棋、围棋）。
   - 它通过维护两个值（alpha 和 beta）来跟踪当前已知的最佳选择，从而剪去不可能影响最终决策的分支。

2. **分支限界法**：
   - 这是一种用于解决组合优化问题（如旅行商问题、背包问题）的算法。
   - 它通过在搜索过程中计算当前路径的下界，并与当前已知的最优解进行比较，来决定是否剪去某个分支。

##### 应用领域

- **人工智能和游戏**：在游戏 AI 中，剪枝算法用于减少需要考虑的可能动作组合。
- **组合优化**：在解决诸如旅行商问题、背包问题等问题时，剪枝算法可以显著减少计算复杂度。
- **决策树**：在机器学习中，剪枝用于简化决策树模型，防止过拟合。

##### 优势

- **提高效率**：通过减少需要评估的节点数量，剪枝算法可以显著加快搜索速度。
- **节省资源**：减少计算和内存使用，使得算法能够处理更大的问题规模。

剪枝算法的核心在于通过合理的评估和决策，**避免不必要的计算**，从而提升算法的整体性能。

有哪些不必要的计算？

- 嵌套循环————双指针

- 在指针移动是根据判定进行移动，而不是先移动再做判断

  ```C++
  class Solution {
  public:
      int maxArea(vector<int>& height) {
          int i=0,j=height.size()-1;
          long max=min(height[0],height[j])*(height.size()-1);
          long ans;
          while(i<j){
              ans=min(height[i],height[j])*(j-i);
              if(ans>max){
                  max=ans;
              }
              if(height[i]<height[j]){
                  i++;
              }else{
                  j--;
              }
              //关键的if—else语句，确定是哪边往哪个方向移动。
          }
          return max;
      }
  };
  ```

  时间复杂度：O(n)

## 4 Leetcode 121 买卖股票的最佳时期

给定一个数组 `prices` ，它的第 `i` 个元素 `prices[i]` 表示一支给定股票第 `i` 天的价格。

你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 `0` 。

方法1：暴力方法

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.size()==1){
            return 0;
        }
        
        int summax=0;
        for(int i=0;i+1<prices.size();i++){
            for(int j=i+1;j<prices.size();j++){
                if(prices[j]-prices[i]>summax){
                    summax=prices[j]-prices[i];
                }
            }
        }
        return summax;
    }
};
```

方法2：优化暴力双循环（枚举剪枝）

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.size()==1){
            return 0;
        }
        
        int summax=0;
        for(int i=0;i+1<prices.size();i++){
            if(i>=1&&prices[i]>prices[i-1]){
                continue;
                //去掉一些完全不可能的情况（枚举剪枝）
            }
            for(int j=i+1;j<prices.size();j++){
                if(j>=1&&prices[j]<prices[j-1]){
                    continue;
                }
                if(prices[j]-prices[i]>summax){
                    summax=prices[j]-prices[i];
                }
            }
        }
        return summax;
    }
};
```

方法3：一次遍历（双指针）

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.size()==1){
            return 0;
        }
        int i=0,j=1;
        //前后指针：j为前置指针，i为后置指针
        int summax=0;
        while(j<prices.size()){
            if(prices[j]<prices[i]){
                i=j;
                //更新i的位置
            }else{
                if(prices[j]-prices[i]>summax){
                    summax=prices[j]-prices[i];
                }
            }
            j++;
        }
        return summax;
    }
};
```

方法4：核心：在任何时刻，最大利润等于当前价格减去最低价格

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        // 初始化一个很大的数值作为初始的股票最低价格
        int inf = 1e9;
        int minprice = inf;
        // 初始化最大利润为0
        int maxprofit = 0;
        
        // 遍历价格数组
        for (int price : prices) {
            // 计算当前的利润（当前价格减去最低价格），并更新最大利润
            maxprofit = max(maxprofit, price - minprice);
            //price-mimprice就是在price之前的所有数的最小值！
            // 更新最低价格
            minprice = min(price, minprice);
        }
        
        // 返回最大利润
        return maxprofit;
    }
};

```

## 5 Leetcode 136 只出现一次的数字

给你一个 **非空** 整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

方法1：哈希表存储（线性时间复杂度&线性空间复杂度）

```C++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        unordered_set<int> judge;
        long sum1=0;
        for(auto num:nums){
            sum1+=num;
            if(judge.find(num)==judge.end()){
                judge.insert(num);
            }else{
                sum1-=2*num;
            }
        }
        return sum1;
    }
};
```

方法2：使用**位运算**！

### 补充：位运算中的异或运算

异或运算（XOR，eXclusive OR）是一种逻辑运算，它在计算机科学和数学中有着广泛的应用。以下是关于异或运算的原理和运算律的简要介绍：

#### 原理

异或运算的基本规则如下：

- **0 XOR 0 = 0**
- **1 XOR 0 = 1**
- **0 XOR 1 = 1**
- **1 XOR 1 = 0**

换句话说，两个操作数不同时结果为1，相同则结果为0。这解释了“eXclusive OR”的含义，即“排他或”。

对于十进制数：a^0=a！

#### 运算律

异或运算具有以下特性：

1. **交换律（Commutative Law）**：
   - `A ^ B = B ^ A`，即异或操作的结果与操作数的顺序无关。

2. **结合律（Associative Law）**：
   - `(A ^ B) ^ C = A ^ (B ^ C)`，这意味着异或操作可以任意分组进行。

3. **恒等律（Identity Law）**：
   - `A ^ 0 = A`，即任何数与0异或，结果还是该数。

4. **自反律（Self-Inverse Law）**：
   - `A ^ A = 0`，任何数与自身异或，结果为0。

5. **零元素（Zero Element）**：
   - `A ^ 1 = ~A`（即A的补码），这表明1在异或运算中起到了一种特殊的作用，它可以翻转操作数的每一位。

6. **分配律（Distributive Law）**：
   - 异或运算不遵循传统意义上的分配律，但它与与运算（AND）有部分分配律：
     - `A ^ (B & C) = (A ^ B) & (A ^ C)`，这在某些情况下可以用于简化计算。

#### 应用

- **数据校验**：异或运算常用于数据校验，如奇偶校验或检验和。
- **加密**：在一些简单的加密算法中使用异或运算，因为它可以隐藏信息。
- **位操作**：在编程中，异或运算可以用来交换变量值（不使用临时变量），翻转特定位，计算汉明距离等。
- **解决问题**：在算法设计中，异或运算可以用于解决一些特殊问题，如找出数组中唯一不重复的元素。

异或运算由于其独特的特性，在计算机编程和数字电路设计中都有广泛的应用，它提供了一种非常高效的方式来处理和操作二进制数据。

所以：**对这个数组的所有数进行位运算**，最后通过位运算的 **结合律和交换律**，一定可以化成a^0=a的形式，其中a就是目标数！

```C++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ret = 0;
        for (auto e: nums) ret ^= e;
        return ret;
    }
};

```

## 6 Leetcode 169 多数元素问题

**非常经典的一道例题！**

给定一个大小为 `n` 的数组 `nums` ，返回其中的多数元素。多数元素是指在数组中出现次数 **大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

#### 方法1：使用哈希表（unordered_map)

时空复杂度均为O(n)

```C++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int n=nums.size();
        int standard=n/2;
        unordered_map<int,int> Mymap;
        for(auto num:nums){
            if(Mymap.find(num)!=Mymap.end()){
                Mymap[num]++;
                if(Mymap[num]>standard){
                    return num;
                }
            }else{
                Mymap.insert(make_pair(num,1));
                if(Mymap[num]>standard){
                    return num;
                }
            }
        }
        return {};
       
    }
};
```

#### 方法2：字符串匹配算法

（详细内容见补充讲义：KMP算法和BM算法）：两种十分高效的字符串检索算法

首先：如果一个数组存在多数元素，**则多数元素唯一**

所以多数元素一定会被当选作为candidate！

**`Boyer-Moore 投票算法`**

```C++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int candidate = 0;  // 假设第一个元素是候选者
        int count = 0;      // 计数器

        for (int num : nums) {
            if (count == 0) {
                // 如果计数器为0，则将当前元素设为新的候选者(因为如果count==0,说明当前的candidate元素肯定不是多数元素！)
                candidate = num;
                //选择了一位新的候选者！
            }
            
            // 如果当前元素与候选者相同，计数器加1；否则减1
            count += (num == candidate) ? 1 : -1;
        }

        // 在结束遍历后，candidate 就是多数元素
        return candidate;
    }
};

```

算法解释：

这个算法基于一个关键的观察：如果一个元素是多数元素，**那么它出现的次数一定超过数组长度的一半**。因此：

- 每次我们遇到一个与当前候选者相同的元素，`count`就增加1，因为这支持了我们的候选者。
- 每次遇到不同的元素，`count`就减1，因为这对当前候选者不利。

由于多数元素出现的次数超过一半，只要我们遍历完整个数组，最后剩下的候选者一定是多数元素。这是因为：

- 当`count`为0时，我们选择新的候选者，这意味着之前的候选者在当前位置之前出现的次数和非候选者出现的次数相等。
- 由于多数元素出现的次数超过一半，在数组结束时，它一定会成为最后的候选者。
- 时间复杂度O(n)，空间复杂度O（1）

算法的弊端：优化了时空复杂度，只依赖一次遍历就解决了问题，但无法解决不存在多数元素的情况！

#### 方法3：分治算法

如果数 a 是数组 nums 的众数，如果我们将 nums 分成两部分，那么 a 必定是至少一部分的众数。

我们可以使用反证法来证明这个结论。假设 a 既不是左半部分的众数，也不是右半部分的众数，那么 a 出现的次数少于 l / 2 + r / 2 次，其中 l 和 r 分别是左半部分和右半部分的长度。由于 l / 2 + r / 2 <= (l + r) / 2，说明 a 也不是数组 nums 的众数，因此出现了矛盾。所以这个结论是正确的。

这样以来，我们就可以使用分治法解决这个问题：将数组分成左右两部分，分别求出左半部分的众数 a1 以及右半部分的众数 a2，随后在 a1 和 a2 中选出正确的众数。

根据众数的唯一性，总能通过类似的方法逼近得到最后的众数（**类似于闭区间套定理**）

```C++ 
class Solution {
    int count_in_range(vector<int>& nums, int target, int lo, int hi) {
        int count = 0;
        for (int i = lo; i <= hi; ++i)
            if (nums[i] == target)
                ++count;
        return count;
    }
    //区间求target出现次数函数
    int majority_element_rec(vector<int>& nums, int lo, int hi) {
        if (lo == hi)
            return nums[lo];
        //递归的结束标志
        int mid = (lo + hi) / 2;
        int left_majority = majority_element_rec(nums, lo, mid);
        int right_majority = majority_element_rec(nums, mid + 1, hi);
        //此处为递归的重复调用！
        if (count_in_range(nums, left_majority, lo, hi) > (hi - lo + 1) / 2)
            return left_majority;
        if (count_in_range(nums, right_majority, lo, hi) > (hi - lo + 1) / 2)
            return right_majority;
        return -1;
    }
public:
    int majorityElement(vector<int>& nums) {
        return majority_element_rec(nums, 0, nums.size() - 1);
    }
};

```

