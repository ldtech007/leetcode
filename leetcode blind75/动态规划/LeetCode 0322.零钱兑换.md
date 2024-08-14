
> *题目链接：* https://leetcode.cn/problems/coin-change/
>
>*视频题解：* https://www.bilibili.com/video/BV1qsvDeHEkg/

# LeetCode 322.零钱兑换

## 题目描述

给你一个整数数组`coins`，表示不同面额的硬币；以及一个整数`amount`，表示总金额。

计算并返回可以凑成总金额所需的**最少的硬币个数**。如果没有任何一种硬币组合能组成总金额，返回`-1` 。

你可以认为每种硬币的数量是无限的。

**举个例子：**

```
输入： coins = [1, 2, 5], amount = 11
输出： 3 
解释： 11 = 5 + 5 + 1
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解推导出原问题的最优解。可以通过两点来判断一个问题能不能通过动态规划来解，一是该问题是否存在递归结构，二是对应的子问题能否记忆化。动态规划可以通过**带备忘录的自上而下的递归**和**自下而上的迭代**来分别实现。由于递归需要用到栈来实现，一些语言对递归的深度是有限制的，所以**自下而上的迭代是动态规划的最佳实现方式**。

## 思路解析

根据题意，零钱兑换的决策树如下图:

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0322-01.png)

根节点表示要兑换的总金额`amount`，每个子节点表示`amount`已经兑换了`coins[i]`（对于图中的例子`0 <= i < 3`）以后剩余要兑换的钱。叶子结点为`0`表示从根节点到叶子结点这条兑换路径是行得通的，叶子结点为负数表示行不通。

实际上本题就是要求在这棵树上所有行得通的兑换路径中，寻找**最短合法路径的长度**。

整个决策树存在递归结构，还存在重复子问题`两个节点2`、`三个节点1`，这些子问题计算一次后可以直接保存下来，避免多次重复计算。这就满足了使用动态规划的条件：**存在递归结构**和**子问题可以记忆化**。所以本题可以用**动态规划**来解。动态规划可以通过**带备忘录的自上而下的递归**和**自下而上的迭代**来分别实现。

### 方法一 自上而下的递归+备忘录

本题单纯的使用递归会超时的。

通过观察上图，可以看出**每个分支存在很多相同的子问题**，比如兑换总金额`2`元，兑换总金额`1`元。我们可以在递归的过程中**使用备忘录把这些子问题的结果保存起来，后面就作为跳出递归的一个条件**，这样可以大大节约时间。

### C++代码

```cpp
class Solution {
public:
  int coinChange(vector<int> &coins, int amount) {
  //定义备忘录
	vector<int> count(amount + 1, INT_MAX);
	count[0] = 0;
  int res = help(coins, count, amount);
	return res;
}

int help(vector<int> &coins, vector<int>& count, int amount) {
    //如果备忘录中已经保存结果
    if (count[amount] < INT_MAX - 1) {
        //直接返回
        return count[amount];
    }
    int coins_len = coins.size();
    int min_res = INT_MAX;
    for (int i = 0; i < coins_len; ++i) {
        if (amount - coins[i] >= 0) {
           //递归遍历（DFS）所以的可能性
            int res = help(coins, count, amount - coins[i]);
            if (res >= 0 && res < min_res) {
                min_res = res + 1;
            }
        }
    }
    //更新备忘录
    count[amount] = min_res == INT_MAX ? -1 : min_res;
    //返回结果
    return count[amount];
}

};
```
### 复杂度分析

**时间复杂度：** 时间复杂度为*O(mn)*，其中`m`为`coins`中元素的个数，`n`为要兑换的总金额。  

**空间复杂度：** 空间复杂度为*O(n)*，`n`为要兑换的总金额。



### 方法二 自下而上的迭代

实现自下而上而上迭代的两个关键步骤：确定**状态转移公式**和**边界情况**。

首先定义`dp[i]`表示兑换金额为`i`，使用`coins`中的零钱兑换所需最少的个数。`dp[i]`的准确定义是状态转移公式成功推导的关键。

针对`coins = [1, 2, 5]`，`amount = 4`。从上面的决策树可以看出可以把兑换`4`块钱分解成兑换`3`元，兑换`2`元，兑换`-1`元，三个子问题。其中`-1`元是无法兑换的，可以直接把这个分支剪掉。可以得到下面的公式：

```cpp
dp[4] = min{dp[3], dp[2]} + 1
```

类似地，把`amount = 4`扩展到`amount = n`，可以得到如下**状态转移公式**：

```cpp
dp[n] = min{dp[n-coins[0]], dp[n-coins[1]], ..., dp[n-coins[m-1]]} + 1
```
其中`m`为`coins`的大小，且需要`n - coins[i] >= 0`，`0 <= i < m`​。

接下来看一下**边界情况**，题目中已经说明`amount = 0`时对应的兑换个数为`0`，所以`dp[0] = 0`。

### C++代码

```cpp
class Solution {
public:
  int coinChange(vector<int> &coins, int amount) {
    int coins_len = coins.size();
    //因为dp是从0开始，要兑换总金额为amount，所以要申请amount+1个元素
    vector<int> dp(amount + 1, amount + 1);
    //边界处理
    dp[0] = 0;
    for (int i = 1; i <= amount; ++i) {
      for (int j = 0; j < coins_len; ++j) {
        //剪掉节点为负的情况
        if (i - coins[j] >= 0) {
          //状态转移公式
          dp[i] = min(dp[i], dp[i-coins[j]] + 1);
        }
      }
    }
    return dp[amount] == amount + 1 ? -1: dp[amount];
  }
};
```
### 复杂度分析

**时间复杂度：** 时间复杂度为*O(m\*n)*，其中`m`为`coins`中元素的个数，`n`为要兑换的总金额。  

**空间复杂度：** 空间复杂度为*O(n)*，`n`为要兑换的总金额。

