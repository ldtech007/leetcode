
> *题目链接：* https://leetcode.cn/problems/house-robber/
>
>*视频题解：* https://www.bilibili.com/video/BV1RwvoeaENG/

# LeetCode 198. 打家劫舍

## 题目描述

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你 **不触动警报装置的情况下** ，一夜之内能够偷窃到的最高金额。

**举个例子：**

```
输入： [1,2,3,1]
输出： 4
解释： 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解推导出原问题的最优解。可以通过两点来判断一个问题能不能通过动态规划来解，一是该问题是否存在递归结构，二是对应的子问题能否记忆化。动态规划可以通过**带备忘录的自上而下的递归**和**自下而上的迭代**来分别实现。由于递归需要用到栈来实现，一些语言对递归的深度是有限制的，所以**自下而上的迭代是动态规划的最佳实现方式**。

## 思路解析

本题是一道经典的动态规划问题，要找到解决动态规划问题的两个突破点：**推导出状态转移公式**和**边界条件处理**。

**首先定义`dp[n]`表示总共有`n`间房所能偷到的最高金额。**

对于`nums=[1, 2, 3, 1]`其所有可能的偷盗路线如下：

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
        vector<int> dp(nums_len + 1, 0);
        if (nums_len > 0) {
            //边界条件
            dp[1] = nums[0];
        }
        if (nums_len > 1) {
            //边界条件
            dp[2] = max(nums[0], nums[1]);
        }
        for (int i = 3; i < nums_len + 1; ++i) {
            //状态转移公式
            dp[i] = max(dp[i-1], dp[i-2] + nums[i-1]);
        }
        return dp[nums_len];
    }
};

```

## java代码

```java
class Solution {
    public int rob(int[] nums) {
        int nums_len = nums.length;
        int[] dp = new int[nums_len + 1];
        
        if (nums_len > 0) {
            // 边界条件
            dp[1] = nums[0];
        }
        if (nums_len > 1) {
            // 边界条件
            dp[2] = Math.max(nums[0], nums[1]);
        }
        
        for (int i = 3; i < nums_len + 1; ++i) {
            // 状态转移公式
            dp[i] = Math.max(dp[i-1], dp[i-2] + nums[i-1]);
        }
        
        return dp[nums_len];
    }
}
```

## python代码

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        nums_len = len(nums)
        dp = [0] * (nums_len + 1)
        
        if nums_len > 0:
            # 边界条件
            dp[1] = nums[0]
        if nums_len > 1:
            # 边界条件
            dp[2] = max(nums[0], nums[1])
        
        for i in range(3, nums_len + 1):
            # 状态转移公式
            dp[i] = max(dp[i-1], dp[i-2] + nums[i-1])
        
        return dp[nums_len]
```

## 复杂度分析

**时间复杂度：** 只需要遍历一遍数组`nums`，所以时间复杂度为*O(n)*，`n`为`nums`的长度。

**空间复杂度：** 需要借助一个`dp`数组，空间复杂度为*O(n)*，`n`为`nums`的长度。

