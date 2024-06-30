
> *题目链接：* https://leetcode.cn/problems/maximum-subarray/
>
> *视频题解：* https://www.bilibili.com/video/BV17q421c7Gs/

# LeetCode 53. 最大子数组和

## 题目描述

给你一个整数数组 `nums` ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

**举个例子：**

```
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6 。
```

## 思路解析

本题可以通过暴力循环来解，但是时间复杂度太大，暴力解法也不是出题的意图。我们还可以通过动态规划来解此题，**动态规划的关键就是推导状态转移公式**，下面我们来一步步推导此题的状态转移公式。

定义`cur_max[i]`为以`nums`中第`i`个元素结尾，子数组和的最大值，其中`0 <= i < nums.size`。在推导`cur_max[i]`的过程中会**枚举以`nums`中任意元素结尾，和最大的子数组**。最终我们从`cur_max[i]`中选择最大的一个即是最终要返回的结果。

1. **考虑数组元素全部都是大于等于`0`的场景**：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0053-01.png)

在这种场景下，因为元素都是正数，不难看出`cur_max[i] = cur_max[i-1] + nums[i]`。

2. **考虑数组元素包含负数的场景**：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0053-02.png)

由于`cur_max[0] = -1`，负数加上一个数会导致和变的更小。那么`cur_max[1] != cur_max[0] + nums[1]`，正确的结果为`cur_max[1] = nums[1]`。进一步优化转移公式为`cur_max[i] = max{cur_max[i-1] + nums[i], nums[i]}`。

## 实现优化

上面的状态转移公式如果用代码来实现需要用到一个跟`nums`长度一致的数组`cur_max`。因为题目要求找到子数组最大的和，我们不需要把每个子数组的状态都保存下来，只保存上一个状态就可以。使用一个整型变量`max_res`来保存全局的最大和。

## C++代码

```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int max_res = nums[0];
        int cur_max = nums[0];
        for(int i = 1; i < nums.size(); ++i){
            //状态转移公式
            cur_max = max(cur_max + nums[i], nums[i]);
            max_res = max(max_res, cur_max);
        }
        return max_res;
    }
};
```

## java代码

```java
class Solution {
    public int maxSubArray(int[] nums) {
        int maxRes = nums[0];
        int curMax = nums[0];
        for (int i = 1; i < nums.length; ++i) {
            //状态转移公式
            curMax = Math.max(curMax + nums[i], nums[i]);
            maxRes = Math.max(maxRes, curMax);
        }
        return maxRes;
    }
}
```

## python代码

```python
 class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        maxRes = nums[0]
        curMax = nums[0]
        for i in range(1, len(nums)):
            #状态转移公式
            curMax = max(curMax + nums[i], nums[i])
            maxRes = max(maxRes, curMax)
        return maxRes
```

## 复杂度分析

**时间复杂度：** 由于只需要遍历一遍`nums`，故时间复杂度为*O(n)*，其中`n`为`nums`的长度。

**空间复杂度：** 整个过程只使用了两个整型变量，所以空间复杂度为*O(1)*。
