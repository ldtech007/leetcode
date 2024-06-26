
> *题目链接：* https://leetcode.cn/problems/maximum-product-subarray/
>
> *视频题解：* https://www.bilibili.com/video/BV1qM4m1Q7nZ/

# LeetCode 152.乘积最大子数组

## 题目描述

给你一个整数数组 `nums` ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

测试用例的答案是一个 **32-位** 整数。

**子数组** 是数组的连续子序列。

**举个例子：**

```
输入: nums = [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

## 思路解析

这是一道经典的动态规划题目，**动态规划的关键就是推导状态转移公式**，接下来带你一步步来推导。

定义`cur_max[i]`表示以`nums`中第`i`个元素结尾子数组乘积最大的值，其中`0 <= i < nums.size`。**在推导`cur_max[i]`的过程中会枚举以`nums`中任意元素结尾，乘积最大的子数组。最终我们从`cur_max[i]`中选择最大的一个即是最终要返回的结果。**

那么`cur_max[i]`要怎么算呢？

1. **数组元素全部都是大于`0`的场景：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0152-01.png)

在这种场景下，因为元素都是正数，不难看出`cur_max[i] = cur_max[i-1] * nums[i]`。

2. **数组元素中含`0`的场景：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0152-02.png)

这个时候，`cur_max[2] = 0`，`nums[3] = 3`，如果继续套用上面的公式算出`cur_max[3] = cur_max[2] * nums[3] = 0`，显然这是不对的，正确的`cur_max[3] = nums[3] = 3`。我们需要修改下上面的公式，`cur_max[i] = max{cur_max[i-1] * nums[i], nums[i]}`。

3. **数组元素包含负数的场景：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0152-03.png)

负数比较特殊，`cur_max[2] = -1`，但`cur_max[3] != max{cur_max[2] * nums[3], nums[3]}`。这是因为**负数的存在可能会导致最大值乘以负数变最小值，最小值乘以负数变最大值**。针对这种情况我们增加一个数组`cur_min[i]`来表示以`nums`中第`i`个元素结尾子数组乘积最小的值，这个时候最终获得的状态转移公式如下。

```cpp
cur_max[i] = max{cur_max[i-1] * nums[i], cur_min[i-1] * nums[i], nums[i]}。
cur_min[i] = min{cur_max[i-1] * nums[i], cur_min[i-1] * nums[i], nums[i]}。
```

## 实现优化

上面的状态转移公式如果用代码来实现需要利用两个跟`nums`长度一致的数组`cur_min`和`cur_max`。因为题目要求找到子数组最大的乘积，我们不需要把每个子数组的状态都保存下来，只保存上一个状态就可以。利用一个整型变量`max_res`来保存全局的最大乘积。

## C++代码

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int nums_len = nums.size();
        int max_res = INT_MIN;
        long long cur_max = 1, cur_min = 1;
        for (int i = 0; i < nums_len; ++i) {
           //提前保存一下cur_max和cur_min前一个状态，避免更新cur_min的时候用了最新状态的cur_max
            long long temp_max = cur_max, temp_min = cur_min;
            //状态转移公式
            cur_max = max(max(temp_max * nums[i], temp_min * nums[i]), (long long)nums[i]);
            cur_min = min(min(temp_max * nums[i], temp_min * nums[i]), (long long)nums[i]);
            if (cur_min < INT_MIN) {
                cur_min = INT_MIN;
            }
            //更新全局最大值
            max_res = max(max_res, (int)cur_max); 
        }
        return max_res;
    }
};
```

## java代码

```java
class Solution {
    public int maxProduct(int[] nums) {
        int nums_len = nums.length;
        int max_res = Integer.MIN_VALUE;
        long cur_max = 1, cur_min = 1;
        for (int i = 0; i < nums_len; ++i) {
            // 提前保存一下cur_max和cur_min前一个状态，避免更新cur_min的时候用了最新状态的cur_max
            long temp_max = cur_max, temp_min = cur_min;
            // 状态转移公式
            cur_max = Math.max(Math.max(temp_max * nums[i], temp_min * nums[i]), nums[i]);
            cur_min = Math.min(Math.min(temp_max * nums[i], temp_min * nums[i]), nums[i]);
            if (cur_min < Integer.MIN_VALUE) {
                cur_min = Integer.MIN_VALUE;
            }
            // 更新全局最大值
            max_res = Math.max(max_res, (int)cur_max);
        }
        return max_res;
    }
}
```

## python代码

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        nums_len = len(nums)
        max_res = float('-inf')
        cur_max = 1
        cur_min = 1
        for i in range(nums_len):
            # 提前保存一下cur_max和cur_min前一个状态，避免更新cur_min的时候用了最新状态的cur_max
            temp_max = cur_max
            temp_min = cur_min
            # 状态转移公式
            cur_max = max(max(temp_max * nums[i], temp_min * nums[i]), nums[i])
            cur_min = min(min(temp_max * nums[i], temp_min * nums[i]), nums[i])
            # 更新全局最大值
            max_res = max(max_res, cur_max)
        return max_res
```

## 复杂度分析

**时间复杂度：** 整个过程只需要遍历一遍数组`nums`，故时间复杂度为*O(n)*，其中`n`为`nums`的长度。

**空间复杂度：** 整个过程只用到了整型变量，故空间复杂度为*O(1)*。

