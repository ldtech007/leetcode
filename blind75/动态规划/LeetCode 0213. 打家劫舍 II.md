
> *题目链接：* https://leetcode.cn/problems/house-robber-ii/

# LeetCode 213. 打家劫舍 II

## 题目描述

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 **围成一圈** ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警** 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **在不触动警报装置的情况下** ，今晚能够偷窃到的最高金额。

**举个例子：**

```
输入： nums = [2,3,2]
输出： 3
解释： 你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解**自下而上**推导出原问题的最优解。

## 思路解析

本题是一道经典的动态规划问题，要找到解决动态规划问题的两个突破点：**推导出状态转移公式**和**边界条件处理**。

本题是「198.打家劫舍」的进阶版，他们之间唯一的区别是***房子变成了首尾相连***，因为`第1间房`和`第n间房`相连，我们需要考虑要不要偷`第1间房`。

1. 如果偷`第1间房`，那么`第n间房`一定不能偷。这个时候我们可以偷的区间就变成`[0,n-1]`。
2. 如果不偷`第1间房`，那么我们可以偷的区间就变成`[1,n]`。

最终就变成了求`[0,n-1]`和`[1,n]`这两个非首尾相连区间能够偷窃到的最高金额，这样就可以复用「198.打家劫舍」的思路。

**首先定义`dp[n]`表示总共有`n`间房所能偷到的最高金额。**

对于**非首尾相连**的数组`nums=[1, 2, 3, 1]`其所以可能的打劫路线如下：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0198-01.png)

根节点到叶子节点就是一条偷盗路线，每个节点表示偷到的总金额，对于上面的例子`dp[4] = 4`。

**状态转移公式需要分两种情况讨论：**

* 偷盗路线包含`第4间房`（`nums[3]`），根据规则这个时候`第3间房`（`nums[2]`）一定是没有被偷的，那么前`2`间房偷到的最大金额加上第`4`间房偷到的金额有可能是前`4`间房偷的最大金额`dp[4] = dp[2] + nums[3]`。
* 偷盗路线不包含`第4间房`（`nums[3]`），这个时候前`3`间房偷到的最大金额有可能也是前`4`间房偷的最大金额`dp[4] = dp[3]`。

上面两种情况选取最大值就可以得到`4`间房偷到的最大金额`dp[4]=max(dp[2] + nums[3], dp[3])`;

扩展到一般情况`dp[n]`可以分解为`dp[n-1]`，`dp[n-2]`**两个子问题的组合**，得到**状态转移公式**：

```cpp
dp[n] = max{dp[n-2] + nums[n-1], dp[n-1]}
```

其中`dp[n-2] + nums[n-1]`表示偷`第n间房`整条路线可以偷到的最大金额。 `dp[n-1]`表示不偷`第n间房`整条路线可以偷到的最大金额。

对于**边界条件** `dp[0] = 0`，`dp[1] = nums[0]`，`dp[2] = max{nums[0], nums[1]}`。

## C++ 代码

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int nums_len = nums.size();
        if (nums_len == 0) return 0;
        if (nums_len == 1) return nums[0];
        return max(help(nums, 0, nums_len - 1), help(nums, 1, nums_len - 1));
    }
    int help(vector<int>& nums, int start, int len) {
        if (len == 0) {
            return 0;
        }
        if (len == 1) {
            return nums[start];
        }
        vector<int> dp(len + 1, 0);
        //边界条件
        dp[1] = nums[start];
        dp[2] = max(nums[start], nums[start + 1]);

        for (int i = start + 3; i < start + len + 1; ++i) {
              //状态转移公式
            dp[i - start] = max(dp[i - start - 1], dp[i - start - 2] + nums[i - 1]);
        }
        return dp[len];
    }
};
```

## 复杂度分析

**时间复杂度：** 只需要遍历两遍数组`nums`，所以时间复杂度为*O(n)*，`n`为`nums`的长度。

**空间复杂度：** 需要借助一个`dp`数组，空间复杂度为*O(n)*，`n`为`nums`的长度。

## 实现优化

上面的空间复杂度是*O(n)*，其实根据状态转移公式的特点，我们可以使用两个整型变量`pre`和`next`来实现`help`函数。

```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int nums_len = nums.size();
        if (nums_len == 0) return 0;
        if (nums_len == 1) return nums[0];
        return max(help(nums, 0, nums_len - 1), help(nums, 1, nums_len - 1));

    }

    int help(vector<int>& nums, int start, int len) {
        if (len == 0) {
            return 0;
        }
        if (len == 1) {
            return nums[start];
        }
        //边界条件
        int pre = nums[start];
        int next = max(nums[start], nums[start + 1]);

        for (int i = start + 2; i < start + len; ++i) {
            //状态转移公式
            int temp = next; 
            next = max(pre + nums[i], next);
            pre = temp;
        }
        return next;
    }
};
```

## 优化后复杂度分析

**时间复杂度：** 只需要遍历两遍数组`nums`，所以时间复杂度为*O(n)*，`n`为`nums`的长度。

**空间复杂度：** 只借助了几个整型变量，空间复杂度为*O(1)*。

