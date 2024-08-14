
> *题目链接：* https://leetcode.cn/problems/word-break/
>
>*视频题解：* https://www.bilibili.com/video/BV1ZSvheYEsk/

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

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解推导出原问题的最优解。可以通过两点来判断一个问题能不能通过动态规划来解，一是该问题是否存在递归结构，二是对应的子问题能否记忆化。动态规划可以通过**带备忘录的自上而下的递归**和**自下而上的迭代**来分别实现。由于递归需要用到栈来实现，一些语言对递归的深度是有限制的，所以**自下而上的迭代是动态规划的最佳实现方式**。

## 思路解析

对于`s = "leetcode"`, `wordDict = ["hello", "leet", "code"]`的决策树如下:

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0139-01.png)

观察决策树，此问题**存在递归结构**且**子问题可以记忆化**。所以本题可以用**动态规划**来解，**动态规划**的两个核心点是**推导状态转移公式**和**边界处理**。

首先定义`dp[i]`为字符串`s`的`[0, i)`区间是否可以利用字典中出现的单词拼接。`dp[i] == true`代表可以。

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
