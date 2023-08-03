
> *题目链接：* https://leetcode.cn/problems/word-break/

# LeetCode 139. 单词拆分

## 题目描述

给你一个字符串 `s` 和一个字符串列表 `wordDict` 作为字典。请你判断是否可以利用字典中出现的单词拼接出 `s` 。

注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。

**举个例子：**

```
输入:  s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解**自下而上**推导出原问题的最优解。

## 思路解析

本题是一道经典的动态规划问题，要找到解决动态规划问题的两个突破点：**推导出状态转移公式**和**边界条件处理**。

首先定义`dp[i]`为字符串`s`的`[0, i)`区间是否可以利用字典中出现的单词拼接。`dp[i] == true`代表可以。

对于`s = "leetcode"`, `wordDict = ["hello", "leet", "code"]`的推导树如下:

![](https://raw.githubusercontent.com/ldtech007/leetcode/main/pic/lc-0139-01.png)

推导的核心思想就是：
* 在遍历`s`的过程中如果满足`wordDict`中的单词`word`和字符串`s`的区间`[i, i + len(word))`组成的子串相同且`dp[i] == true`，就更新`dp[i+len(word)] = dp[i]`。

所以**状态转移公式** 就是`dp[i+len(word)] = dp[i]`。
定义**边界条件** `dp[0] = true`。

对于上面的例子`dp[8] = dp[4] = dp[0] = true`。

## C++代码

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {

        int s_len = s.length();
        //定义dp
        vector<bool> dp(s_len + 1, false);
        //边界条件
        dp[0] = true;
        for (int i = 0; i < s_len; ++i) {
            for (auto& word:wordDict) {
                int word_len = word.length();
                if (dp[i] && (i + word_len <= s_len) && s.substr(i, word_len) == word) {
                    //状态转移公式
                    dp[i + word_len] = dp[i];
                }
            }
        }
        return dp[s_len];
    }
};
```
## 复杂度分析

**时间复杂度：** *O(mn)*，其中`m`为`s`的长度，`n`为`wordDict`的长度。

**空间复杂度：** *O(m)*，其中`m`为`s`的长度。
