
# LeetCode 15.三数之和

> *题目链接：* https://leetcode.cn/problems/3sum/

## 题目描述

给你一个整数数组 `nums` ，判断是否存在三元组 `[nums[i], nums[j], nums[k]]` 满足 `i != j`、`i != k` 且 `j != k` ，同时还满足 `nums[i] + nums[j] + nums[k] == 0` 。请你返回所有和为 `0` 且不重复的三元组。

**注意：** 答案中不可以包含重复的三元组。

**举个例子：**

```
输入： nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

## 思路解析

本题是经典的双指针类型的问题，解决此问题的核心在于以下两点。

1. 左右指针的移动策略。

2. 结果的去重策略。

首先对数组`nums`进行**升序排序**，类似「167.两数之和II」，本次使用三个索引：`i`，`left`，`right`。初始值`i = 0`，`left = i + 1`，`right = nums.size - 1`。

**算法如下：**

1. **`nums[i] + nums[left] + nums[right] > target`时**，`nums[left]`或`nums[right]`需要一个更小的值，由于`nums`是升序的，故**只能把索引`right`往左移，`--right`**。如果`left >= right`，向右移动`i`，直到`nums[i] != nums[i-1]`（对`nums[i]`去重）。

2. **`nums[i] + nums[left] + nums[right] < target`时**，`nums[left]`或`nums[right]`需要一个更大的值，由于`nums`是升序的，故**只能把索引`left`往右移，`++left`**。如果`left >= right`，向右移动`i`，直到`nums[i] != nums[i-1]`（对`nums[i]`去重）。

3. **`nums[i] + nums[left] + nums[right] == target`时，同时移动`left`和`right`**，`left`右移直到`nums[left-1] != nums [left]`，`right`左移直到`nums[right] != nums[right+1]`(对`nums[left]`和`nums[right]`去重)。

4. 如果`i == nums.size()`，算法结束。

对于`nums = [-1,0,1,2,-1,-4]`，流程如下图：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0015-01.png)

## C++代码

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        int nums_len = nums.size();
        sort(nums.begin(),nums.end());
       
        for(int i = 0; i< nums_len; ++i) {
           //去重
            if (i > 0 && nums[i]==nums[i-1])
                continue;

            int left = i + 1, right = nums_len - 1;
            while (left < right) {
                if (nums[i] + nums[left] + nums[right] == 0) {
                    res.push_back({nums[i], nums[left], nums[right]});
                    ++left;
                    //去重 [-2,-2,0,0,2,2]
                    //      i     L     R
                    //避免[-2,0,2]多次被加到res中
                    while(left < right && nums[left-1]==nums[left])
                        ++left;
                    
                    --right;
                    //去重
                    while(left < right && nums[right]==nums[right+1])
                       --right;
    
                } else if (nums[i] + nums[left] + nums[right] < 0) {
                    ++left;
                } else {
                    --right;
                }
            }
        }
        return res;
    }
};
```

## 复杂度分析

**时间复杂度：** 首先排序最快 *O(nlogn)*，然后索引`i`遍历一遍字符串`nums`，针对每个`i`，都有`left`和`right`共同遍历一遍`nums`，总共需要 *O(n<sup>2</sup>)*，故时间复杂度是 *(nlogn)* + *O(n<sup>2</sup>)* = *O(n<sup>2</sup>)*。

**空间复杂度：** 主要是排序消耗的空间，看具体的排序算法，快排的话是*O(1)*，寻找三数之和的过程本身空间复杂度也是*O(1)*，故总的空间复杂度是*O(1)*。

