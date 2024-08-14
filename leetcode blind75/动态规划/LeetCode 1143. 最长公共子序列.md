
> *题目链接：* https://leetcode.cn/problems/longest-common-subsequence/
>
>*视频题解：* https://www.bilibili.com/video/BV1RovheZEHf/

# LeetCode 1143. 最长公共子序列

## 题目描述

给定两个字符串 `text1` 和 `text2`，返回这两个字符串的最长 **公共子序列** 的长度。如果不存在 **公共子序列** ，返回 `0` 。

一个字符串的 **子序列** 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。

* 例如，`"ace"` 是 `"abcde"` 的子序列，但 `"aec"` 不是 `"abcde"` 的子序列。

两个字符串的 **公共子序列** 是这两个字符串所共同拥有的子序列。

**举个例子：**

```
输入： text1 = "abcde", text2 = "ace" 
输出： 3  
解释： 最长公共子序列是 "ace" ，它的长度为 3 。
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解推导出原问题的最优解。可以通过两点来判断一个问题能不能通过动态规划来解，一是该问题是否存在递归结构，二是对应的子问题能否记忆化。动态规划可以通过**带备忘录的自上而下的递归**和**自下而上的迭代**来分别实现。由于递归需要用到栈来实现，一些语言对递归的深度是有限制的，所以**自下而上的迭代是动态规划的最佳实现方式**。

## 思路解析

本题是经典的二维动态规划问题，要找到解决动态规划问题的两个突破点：**推导出状态转移公式**和**边界条件处理**。

首先定义`dp[i][j]`为`text1`的`[0, i)`区间和`text2`的`[0, j)`区间的最长公共子序列。`[0, i)`区间的长度为`i`，`[0, j)`区间的长度为`j`。

接下来我们来看两种情况下的**子问题分解**：
1. `text1[i-1] == text2[j-1]`，这个时候`text1`的`[0, i)`区间和`text2`的`[0, j)`区间上的最长公共子序列就变成了 ***`text1`的`[0, i-1)`区间和`text2`的`[0, j-1)`区间上的最长公共子序列加`1`*** 。即`dp[i][j] = dp[i-1][j-1] + 1`。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-1143-01.png)

2. `text1[i-1] != text2[j-1]`，这个时候`text1`的`[0, i)`区间和`text2`的`[0, j)`区间上的最长公共子序列就变成了 ***`text1`的`[0, i-1)`区间和`text2`的`[0, j)`区间上的最长公共子序列*** 以及 ***`text1`的`[0, i)`区间和`text2`的`[0, j-1)`区间上的最长公共子序列*** 中比较长的一个。即`dp[i][j] = max(dp[i-1][j], dp[i][j-1])`。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-1143-02.png)

由于整个推导过程是**自下而上**的，在求`dp[i][j]`的时候`dp[i-1][j-1]`，`dp[i][j-1]`，`dp[i-1][j]`都是已经推出结果的。

所以**状态转移公式**为：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-1143-03.png)

对于**边界条件**，很明显`dp[i][0] = 0`且`dp[j][0] = 0`。

`text1 = "abcde"`， `text2 = "ace"`的推导过程如下图：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-1143-04.png)

最终`dp[5][3] = 3`，最长公共子序列的长度为`3`。

## C++代码

```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int text1_len = text1.length();
        int text2_len = text2.length();
        //定义二维dp数组，并初始化，dp[i][0]=0 dp[0][j]=0
        vector<vector<int>> dp(text1_len + 1, vector<int>(text2_len + 1, 0));

        for (int i = 0; i < text1_len; ++i) {
            for (int j = 0; j < text2_len; ++j) {
                //text1[i] == text2[j]
                if (text1[i] == text2[j]) {
                    //状态转移公式
                    dp[i+1][j+1] = dp[i][j] + 1;
                 //text1[i] != text2[j]
                } else {
                    //状态转移公式
                    dp[i+1][j+1] = max(dp[i+1][j], dp[i][j+1]);
                }
            }
        }  
        return dp[text1_len][text2_len];
    }
};

```
## 复杂度分析

**时间复杂度：** *O(mn)* ，其中`m`为`text1`的长度，`n`为`text2`的长度。

**空间复杂度：** *O(mn)* ，其中`m`为`text1`的长度，`n`为`text2`的长度。
