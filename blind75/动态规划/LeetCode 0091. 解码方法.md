
> *题目链接：* https://leetcode.cn/problems/decode-ways/

# LeetCode 91. 解码方法

## 题目描述

一条包含字母 `A-Z` 的消息通过以下映射进行了 **编码** ：

```
'A' -> "1"
'B' -> "2"
...
'Z' -> "26"
```

要 **解码** 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，`"11106"` 可以映射为：
* `"AAJF"` ，将消息分组为 `(1 1 10 6)`
* `"KJF"` ，将消息分组为 `(11 10 6)`

注意，消息不能分组为  `(1 11 06)` ，因为 `"06"` 不能映射为 `"F"` ，这是由于 `"6"` 和 `"06"` 在映射中并不等价。

给你一个只含数字的 **非空** 字符串 `s` ，请计算并返回 **解码** 方法的 **总数** 。

题目数据保证答案肯定是一个 **32 位** 的整数。

**举个例子：**

```
输入： s = "12"
输出： 2
解释： 它可以解码为 "AB"（1 2）或者 "L"（12）。
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解来推导出原问题的最优解。

**回溯**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于求解组合问题、排列问题等。回溯的核心思想是**通过递归的方式枚举所有可能的解**，直到找到符合条件的解为止。回溯算法通常使用递归函数来实现，每次递归时都会对当前状态进行判断，如果符合条件则继续递归，否则回溯到上一层状态。

动态规划和回溯的相似之处在于它们都将原问题分解为子问题来求解，但它们的不同之处在于动态规划通常用于**自下而上**求解最优化问题，而回溯通常用于**自上而下**求解所有可能的解。

## 思路解析

### 方法1 动态规划

我们先介绍动态规划的解法，动态规划的两个关键步骤：**状态转移公式推导**和**边界情况处理**。
首先定义`dp[i]`为`s`前`i`字符，即区间`[0,i)`上解码方法的总数。

动态规划是一种**自下而上**的推导方式，在我们求`dp[i]`时，其实子问题`dp[i-1]`和`dp[i-2]`已经被推导出来了。

因为一个字符被编码后其范围在`"1"~"26"`之间，我们需要对`s[i-1]`，`s[i-2]`分情况进行分析。
1. `s[i-1]`被单独解码，如果`s[i-1]`在`'1'~'9'`范围内，可以被单独解码，这个时候被解码的数量`dp[i]`等于`dp[i-1]`。
2. `s[i-2]s[i-1]`一起被解码，如果`s[i-2]s[i-1]`在`"10"~"26"`范围内，可以被组合解码，这个时候被解码的数量`dp[i]`等于`dp[i-2]`。
3. 如果`1`和`2`中的条件都满足，那么被解码的数量`dp[i]`等于`dp[i-1] + dp[i-2]`。

由上面的分析可以知道**状态转移公式**如下：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0091-01.png)

对于**边界条件** `dp[0] = 1`，因为空字符串也可以被解码为空字符串。

### C++代码

```cpp
class Solution {
public:
    int numDecodings(string s) {
        int s_len = s.length();
        vector<int> dp(s_len + 1, 0);
        //边界条件处理
        dp[0] = 1;
        for(int i = 1; i <= s_len; ++i) {
            //状态转移公式
            if (s[i - 1] != '0') dp[i] = dp[i - 1];
            if (i - 1 > 0 && ((s[i - 2] == '1') || (s[i - 2] == '2' && s[i - 1] >= '0' && s[i - 1] <= '6'))) {
                //状态转移公式
                dp[i] += dp[i - 2];
            }
        }
        return dp[s_len];
    }

};
```
### 复杂度分析

**时间复杂度：** *O(n)*，`n`为`s`的长度。

**空间复杂度：** *O(n)*，`n`为`s`的长度。

### 方法2 回溯+备忘录

回溯的关键在于**划分子问题**和**确定回溯条件**。

回溯是一种**自上而下**的推导方式，在我们要求`s`的`[i,n)`区间解码数量时，需要把问题拆解成**独立的子问题**：
1. 如果`s[i]`在`'1'~'9'`范围内，可以被单独解码，这个时候被解码的数量其实就是子问题`[i+1,n)`区间解码数量。
2. 如果`s[i]s[i+1]`在`"10"~"26"`范围内，可以被组合解码，这个时候被解码的数量其实就是子问题`[i+2,n)`区间解码数量。
3. 如果`1`和`2`中的条件都满足，那么被解码的数量就是子问题`[i+1,n)`区间和子问题`[i+2,n)`区间解码数量之和。

对于上述过程我们增加一个**备忘录**来保存子问题的解，可以避免多次去计算同一个子问题的解，提高效率。

**回溯条件**就是**子问题区间为空**或**备忘录中已经保存了子问题的解**。

### C++代码

```cpp
class Solution {
public:
    int numDecodings(string s) {
        map<int, int> count;
        count[s.length()] = 1;
        return dfs(s, count, 0);
    }

    int dfs(string& s, map<int, int>& count, int i) {
        //回溯条件
        if (count.find(i) != count.end()) {
            return count[i];
        }
        //回溯条件
        if (s[i] == '0') {
            return 0;
        }
        //子问题处理
        int res = dfs(s, count, i + 1);
        if (i + 1 < s.length() && (s[i] == '1' || (s[i] == '2'&& s[i + 1] >= '0' && s[i + 1] <= '6'))) {
            //子问题处理
            res += dfs(s, count, i + 2);
        }
        //更新备忘录
        count[i] = res;
        return res;
    }
};
```

### 复杂度分析

**时间复杂度：** *O(n)*，`n`为`s`的长度。

**空间复杂度：** *O(n)*，`n`为`s`的长度。
