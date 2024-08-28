> *题目链接：* https://leetcode.cn/problems/longest-consecutive-sequence/

# LeetCode 128. 最长连续序列

## 题目描述

给定一个未排序的整数数组 `nums` ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 `O(n)` 的算法解决此问题。

**举个例子：**

```
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

## 思路解析

本题的思路比较类似于广度优先搜索。

1. 把数组`nums`的所有元素存到`hashset`中。
2. 遍历数组`nums`，明确**连续序列**的起点，对于数组`nums`中的元素`num`，如果`num - 1`不在`hashset`中，就把`num`作为一个连续序列的起点，进入`步骤3`。
3. 从连续序列的起点`num`开始，不断去`hashset`中寻找`num + i`，`i >= 1`。直到`num + len`不在`hashset`中(以`num`为起点的序列此后就不再连续)，更新最长连续序列的长度`max_len = max(max_len, len)`。如果数组`nums`已遍历完，进入`步骤4`，否则进入`步骤2`。
4. 返回`max_len`。

## C++代码

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        int max_len = 0;
        unordered_set<long long> nums_set;
        int nums_len = nums.size();
        //把所有的元素保存在set中
        for (int i = 0; i < nums_len; ++i) {
            nums_set.emplace(nums[i]);
        }

        for (auto& num:nums) {
            //num-1不在set中 保证num是连续序列的起始元素
            if (nums_set.find(num - 1) == nums_set.end()) {
                int len = 0;
                //num+len在set中 说明num～num+len是连续的
                while (nums_set.find(num + len) != nums_set.end()) {
                    ++len;
                    max_len = max(len, max_len);
                }
            }
        }
        return max_len;
    }
};
```

## java代码

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        int maxLen = 0;
        Set<Long> numsSet = new HashSet<>();
        int numsLen = nums.length;
        // 把所有的元素保存在set中
        for (int i = 0; i < numsLen; i++) {
            numsSet.add((long) nums[i]);
        }

        for (int num : nums) {
            // num-1不在set中 保证num是连续序列的起始元素
            if (!numsSet.contains((long) num - 1)) {
                int len = 0;
                // num+len在set中 说明num～num+len是连续的
                while (numsSet.contains((long) num + len)) {
                    len++;
                    maxLen = Math.max(len, maxLen);
                }
            }
        }
        return maxLen;
    }
}
```

## python代码

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        max_len = 0
        nums_set = set(nums)
        nums_len = len(nums)
        # 把所有的元素保存在set中
        for num in nums:
            # num-1不在set中 保证num是连续序列的起始元素
            if num - 1 not in nums_set:
                length = 0
                # num+len在set中 说明num～num+len是连续的
                while num + length in nums_set:
                    length += 1
                max_len = max(length, max_len)
        return max_len
```

## 复杂度分析

**时间复杂度：** *O(n)*，只需要遍历一遍数组，其中`n`为数组的大小。

**空间复杂度：** *O(n)*，需要借助一个hashset，其中`n`为数组的大小。


