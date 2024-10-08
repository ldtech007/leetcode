
> *题目链接：* https://leetcode.cn/problems/climbing-stairs/
>
>*视频题解：* https://www.bilibili.com/video/BV1h1421t7W3/

# LeetCode 70.爬楼梯

## 题目描述

假设你正在爬楼梯。需要`n`阶你才能到达楼顶。
每次你可以爬`1`或`2`个台阶。你有多少种不同的方法可以爬到楼顶呢？

**举个例子：**

```
输入： n = 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1. 1阶 + 1阶
2. 2阶
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解推导出原问题的最优解。可以通过两点来判断一个问题能不能通过动态规划来解，一是该问题是否存在递归结构，二是对应的子问题能否记忆化。动态规划可以通过**带备忘录的自上而下的递归**和**自下而上的迭代**来分别实现。由于递归需要用到栈来实现，一些语言对递归的深度是有限制的，所以**自下而上的迭代是动态规划的最佳实现方式**。

## 思路解析

假设只有`4`个台阶，`0`阶代表地面。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0070-01.png)

整个爬楼梯的过程对应的决策树可以表示成下图：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0070-02.png)

**每个节点的值表示剩余的台阶数**，从根节点到叶子结点组成的路径代表一种爬楼梯的方法。

整个决策树存在递归结构，还存在重复子问题`两个节点2`、`三个节点1`，这些子问题计算一次后可以直接保存下来，避免多次重复计算。这就满足了使用动态规划的条件：**存在递归结构**和**子问题可以记忆化**。所以本题可以用**动态规划**来解，**动态规划**的两个核心点是**推导状态转移公式**和**边界处理**。

**定义`dp[i]`为`i`个台阶对应的爬楼梯的方法个数**。`dp[i]`的准确定义是推导状态转移公式的关键。

因为每次只能爬`1`或`2`个台阶，从上面的图可以看出`4`个台阶的爬楼梯方法可以拆解成`3`和`2`个台阶爬楼梯方法之和。说白了就是`节点4`到叶子`节点0`的所有路径等于`节点3`和`节点2`到叶子节点路径的总和。
$$dp[4] = dp[3] + dp[2]$$

由`4`个台阶进一步扩展到`n`个台阶，我们得到下面的**状态转移公式**：
$$dp[i] = dp[i-1] + dp[i-2], 1 < i <= n$$

**接下来进行边界处理**，根据上面的公式可知 $dp[2] = dp[1] + dp[0]$，很显然`dp[1] = 1`，`dp[2] = 2`，上面也说到`0`阶代表地面，为了写代码方便这里我们定义`dp[0] = 1`。

## C++代码

```cpp
class Solution {
public:
    int climbStairs(int n) {
        //因为有n阶台阶，台阶从0开始计算，所以定义n+1个元素
        vector<int> dp(n+1, 0);
        //定义边界dp[0]=1
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; ++i) {
            //状态转移公式
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
};
```

## java代码

```java
class Solution {
    public int climbStairs(int n) {
        // 因为有n阶台阶，台阶从0开始计算，所以定义n+1个元素
        int[] dp = new int[n + 1];
        // 定义边界dp[0]=1
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; ++i) {
            // 状态转移公式
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
}
```

## python代码

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        # 因为有n阶台阶，台阶从0开始计算，所以定义n+1个元素
        dp = [0] * (n + 1)
        # 定义边界dp[0]=1
        dp[0] = 1
        dp[1] = 1
        for i in range(2, n + 1):
            # 状态转移公式
            dp[i] = dp[i-1] + dp[i-2]
        return dp[n]
```

上面的代码实现使用了一个长为`n+1`的`dp`数组，我们观察状态转移公式的规律，其实并不需要保存每个台阶数为`i`的爬楼梯方法，可以通过两个整型变量来优化上面的实现。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0070-03.png)

## c++代码

```cpp
class Solution {
public:
    int climbStairs(int n) {
        int pre = 1;
        int next = 1;
        for (int i = 2; i <= n; ++i) {
            int temp = next;
            next = pre + next;
            pre = temp;
        }
        return next;
    }
};
```

## java代码

```java
class Solution {
    public int climbStairs(int n) {
        int pre = 1;
        int next = 1;
        for (int i = 2; i <= n; ++i) {
            int temp = next;
            next = pre + next;
            pre = temp;
        }
        return next;
    }
}
```

## python代码

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        pre = 1
        next = 1
        for i in range(2, n + 1):
            temp = next
            next = pre + next
            pre = temp
        return next
```

## 复杂度分析

**时间复杂度：** 两种实现方式的时间复杂度都是*O(n)*，`n`为台阶的个数。

**空间复杂度：** 第一种实现方式的空间复杂度为*O(n)*，`n`为台阶的个数。第二种实现方式的空间复杂度为*O(1)*。

