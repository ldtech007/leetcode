
> *题目链接：* https://leetcode.cn/problems/longest-increasing-subsequence/

# LeetCode 300. 最长递增子序列

## 题目描述

给你一个整数数组`nums` ，找到其中最长严格递增子序列的长度。

**子序列**是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，`[3,6,2,7]`是数组`[0,3,1,6,2,2,7]`的子序列。

**举个例子：**

```
输入： nums = [10,9,2,5,3,7,101,18]
输出： 4
解释： 最长递增子序列是[2,3,7,101]，因此长度为 4。
```

## 知识回顾

**动态规划**是一种通过将原问题分解为子问题来求解复杂问题的算法思想。它通常用于**求解最优化问题**，例如最长公共子序列、背包问题等。动态规划的核心思想是将原问题分解为若干个子问题，通过求解子问题的最优解**自下而上**推导出原问题的最优解。

## 思路解析

### 方法一 动态规划

上面说到动态规划是**自下而上**推导的过程，下图给出了`nums=[1, 4, 3]`自下而上推倒最长递增子序列的过程。

![](https://raw.githubusercontent.com/ldtech007/leetcode/main/pic/lc-0300-01.png)

从上图可以看出题目其实就是要求从**根节点**到**合法叶子节点**的最长路径，其中**合法叶子节点**是叶子节点中的数组是递增序列。

下面介绍一下动态规划的解法。

动态规划的核心步骤是**推倒状态转移公式**和**边界条件处理**。

首先定义`dp[i]`表示以`nums`第`i`个元素结尾的最长递增子序列的长度。

以上面`nums=[1, 4, 3]`这个例子我们来推导下**状态转移公式**。从上图红色的虚框可以看出`dp[2] = max{dp[0] + 1, dp[2]}`。过滤掉了`dp[1] + 1`的情况，因为`nums[2] < nums[1]`，这样组成的子序列是非法的（图中打叉的节点）。

扩展到一般情况可以得到状态转移公式：
$$dp[n] = max\{dp[0] + 1, ...,dp[i] + 1，dp[n]\}$$
其中`0 <= i < n`，且`nums[n] > nums[i]`。

接下来看下**边界条件**，`nums`的每个元素都各自为一个长度为`1`的子序列，故初始定义`dp[i] = 1`。

最终`dp`中最大的元素就是我们要找的答案。

### C++代码

```cpp
class Solution {
public:
  int lengthOfLIS(vector<int> & nums) {
    int nums_len = nums.size();
    //边界条件初始化
    vector<int> dp(nums_len, 1);
    for (int i = 0; i < nums_len; ++i) {
      for (int j = 0; j < i; ++j) {
        //状态转移公式
        if (nums[i] > nums[j]) {
          dp[i] = max(dp[i], dp[j] + 1);
        }
      }
    }
    return *max_element(dp.begin(), dp.end());
  }
};
```
### 复杂度分析

**时间复杂度:** *O(n<sup>2</sup>)*，其中`n`为`nums`的长度。

**空间复杂度：** *O(n)*， 其中`n`为`nums`的长度。

接下来介绍一种时间复杂度更低的方法。

### 方法二 二分法

定义一个数组`tail`，遍历完`nums`，保证`tail[i]`是`nums`所有长度为`i`的递增子序列中最后一个元素的最小值，最终`tail`的长度就是最长递增子序列的大小。

比如`nums=[1,2,5,4]`，`[1,2,5]`和`[1,2,4]`都是`nums`长度为`3`的递增子序列，他们的最后一个元素是`5`和`4`，这个时候`tail[3]=4`。

实现这个算法需要用到二分查找，步骤如下：

* 遍历`nums`，如果`nums[i] > tail[i]`就把`nums[i]`加到`tail`后面。否则就用`nums[i]`替换第一个大于`nums[i]`的`tail[j]`（二分查找），`0 <= j <= i`。

上面的步骤始终保证`tail`是单调递增的，且`tail`最后一个元素足够小，更容易满足`nums[i] > tail.back()`，使`tail`长度更长。进而得到最长的递增子序列长度。

### C++代码

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        //用来存储递增的元素
        vector<int> tail;
        for(auto& num:nums){
            if(tail.size()==0 || num>tail.back()){
                tail.emplace_back(num);
                continue;
            }
            //通过二分查找获取tail中第一个大于num的元素
            int left = 0,right = tail.size()-1;
            while(left<right){
                int mid = (left + right)/2;
                if(tail[mid]<num){
                    left = mid+1;
                }else{
                    right = mid;
                }
            }
            tail[left] = num;
        }
        //返回最长递增子序列的长度
        return tail.size();
    }
};
```
### 复杂度分析

**时间复杂度:** *O(nlogn)*，其中`n`为`nums`的长度。

**空间复杂度：** *O(n)*， 其中`n`为`nums`的长度。
