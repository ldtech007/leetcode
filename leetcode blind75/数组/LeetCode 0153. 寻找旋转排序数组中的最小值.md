
> *题目链接：* https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/
>
> *视频题解：* https://www.bilibili.com/video/BV1VC41157ha/

# LeetCode 153. 寻找旋转排序数组中的最小值

## 题目描述

已知一个长度为 `n` 的数组，预先按照升序排列，经由 `1` 到 `n` 次 **旋转** 后，得到输入数组。例如，原数组 `nums = [0,1,2,4,5,6,7]` 在变化后可能得到：
若旋转 `4` 次，则可以得到 `[4,5,6,7,0,1,2]`
若旋转 `7` 次，则可以得到 `[0,1,2,4,5,6,7]`
注意，数组 `[a[0], a[1], a[2], ..., a[n-1]]` **旋转一次** 的结果为数组 `[a[n-1], a[0], a[1], a[2], ..., a[n-2]]` 。

给你一个元素值 **互不相同** 的数组 `nums` ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 **最小元素** 。

你必须设计一个时间复杂度为 *O(log n)* 的算法解决此问题。

**举个例子：**

```
输入：nums = [3,4,5,1,2]
输出：1
解释：原数组为 [1,2,3,4,5] ，旋转 3 次得到输入数组。
```

## 思路解析

题目要求要在 *O(log n)* 的时间复杂度完成解答，自然而然我们会想到使用 **二分查找** 来解决此问题。`[L, R]`来表示我们要查找的区间，区间的中间索引 `M = (L + R) / 2`。

旋转排序数组有个特点，**只需要比较区间两个边界的元素的大小，就可以知道区间是否是单调的**。比如，如果`nums[L] < nums[M]`，那么区间`[L, M)`就是升序的。

分析题意其实不难发现，寻找最小的元素只存在下面三种情况。

1. **数组旋转后的顺序没有变化。**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0153-04.png)

这种情况下`nums[L] <= nums[M] <= nums[R]`，最小值为`nums[L]`。

2. **数组旋转后最小值在索引 `M` 的右侧。**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0153-05.png)

这种情况下`nums[L] <= nums[M] >= nums[R]`，`[L, M]`为升序，最小值只能在 `M` 的右边，故更改左边界`L = M + 1`。 

3. **数组旋转后最小值在索引 `M` 的左侧。**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0153-06.png)

这种情况下`nums[L] >= nums[M]`，`nums[R] >= nums[M]`, `[M, R]`为升序，最小值只能在 `M` 的左边(包含`M`)，故更改右边界`R = M`。

根据上面三种情况，代码如下。

## C++代码

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {

        int left = 0, right = nums.size() - 1;
        int mid;
        while (left <= right) {
            mid = (left + right) / 2;
            // 区间[left,right]收缩到了一个递增的区间
            if (nums[mid] >= nums[left] && nums[right] >= nums[mid]) {
                return nums[left];
            //最小值在索引mid的右侧
            } else if (nums[mid] >= nums[left]) {
                left = mid + 1;
            //最小值在索引mid的左侧
            } else if (nums[mid] <= nums[right]) {
                right = mid;
            }
        } 
        return -1;
    }
};
```

## java代码

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        int mid;
        while (left <= right) {
            mid = (left + right) / 2;
            // 区间[left,right]收缩到了一个递增的区间
            if (nums[mid] >= nums[left] && nums[right] >= nums[mid]) {
                return nums[left];
            //最小值在索引mid的右侧
            } else if (nums[mid] >= nums[left]) {
                left = mid + 1;
            //最小值在索引mid的左侧
            } else if (nums[mid] <= nums[right]) {
                right = mid;
            }
        } 
        return -1;
    }
}
```

## python代码

```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = 0, len(nums) - 1
        while left <= right:
            mid = (left + right) // 2
            # 区间[left,right]收缩到了一个递增的区间
            if nums[mid] >= nums[left] and nums[right] >= nums[mid]:
                return nums[left]
            #最小值在索引mid的右侧
            elif nums[mid] >= nums[left]:
                left = mid + 1
            #最小值在索引mid的左侧
            elif nums[mid] <= nums[right]:
                right = mid
        return -1
```

## 复杂度分析

**时间复杂度：** 由于使用了二分查找，故时间复杂度为 *O(log n)* ，其中 `n` 为数组的长度。

**空间复杂度：** 整个过程就使用了三个整型变量，故空间复杂度为 *O(1)* 。
