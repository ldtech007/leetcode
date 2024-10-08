
> *题目链接：* https://leetcode.cn/problems/jump-game/
>
>*视频题解：* https://www.bilibili.com/video/BV1gwYKekEVN/

# LeetCode 55. 跳跃游戏

## 题目描述

给定一个非负整数数组 `nums` ，你最初位于数组的 **第一个下标** 。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标。

**举个例子：**

```
输入：nums = [2,3,1,1,4]
输出：true
解释：可以先跳 1 步，从下标 0 到达下标 1, 然后再从下标 1 跳 3 步到达最后一个下标。
```

## 知识回顾

**贪心算法**是一种常见的算法思想，它通常用于解决优化问题。贪心算法的基本思想是，在每一步选择中都采取当前状态下最优的选择，从而希望最终得到全局最优解。

## 思路解析

本题是一道贪心算法应用的经典问题。应用贪心算法的关键就是**每一步都采取当前状态下的最优选择**。

本题的算法如下：

1. 逆序遍历`nums`，`target=nums.size()`。
2. 遍历到索引`i`，跳`nums[i]`步，`i + nums[i] >= target`说明子目标可达，此时更新`target = i`。
3. 最终`target == 0`说明总目标可达。

上述步骤把总目标拆解成一个个子目标，为达成每个子目标都采取当下能跳的最大长度，这就很符合贪心的**每一步都采取当前状态下的最优选择**这个原则。

## C++代码

```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int nums_len = nums.size();
        //初始化目标值
        int target = nums_len - 1;
        for (int i = nums_len - 1; i >=0; --i) {
            //当前目标可达，更新目标
            if (i + nums[i] >= target) {
                target = i;
            }
        }

        if (target == 0) {
            return true;
        }
        return false;
    }
};
```

## java代码

```java
class Solution {
    public boolean canJump(int[] nums) {
        int nums_len = nums.length;
        // 初始化目标值
        int target = nums_len - 1;
        for (int i = nums_len - 1; i >= 0; --i) {
            // 当前目标可达，更新目标
            if (i + nums[i] >= target) {
                target = i;
            }
        }

        if (target == 0) {
            return true;
        }
        return false;
    }
}
```

## python代码

```python
class Solution:
    def canJump(self, nums: List[int]) -> bool:
        nums_len = len(nums)
        # 初始化目标值
        target = nums_len - 1
        for i in range(nums_len - 1, -1, -1):
            # 当前目标可达，更新目标
            if i + nums[i] >= target:
                target = i

        if target == 0:
            return True
        return False
```

## 复杂度分析

**时间复杂度：** *O(n)*，`n`为`nums`的长度。

**空间复杂度：** *O(1)*。
