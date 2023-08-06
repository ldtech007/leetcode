
> *题目链接：* https://leetcode.cn/problems/container-with-most-water/

# LeetCode 11.盛最多水的容器

## 题目描述

给定一个长度为 `n` 的整数数组 `height` 。有 `n` 条垂线，第 `i` 条线的两个端点是 `(i, 0)` 和 `(i, height[i])` 。

找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

**说明：** 你不能倾斜容器。

**举个例子：**

![](../../pic/lc-0011-01.png)

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49 
解释：图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。
```

## 思路解析

本题可以使用暴力枚举边界的方式来计算最大值，但是时间复杂度为 *O(n<sup>2</sup>)*，`oj`会判定超时。

下面介绍一个使用双索引的解法。

定义索引`L`为容器的左边界，索引`R`为容器的右边界。那么区间`[L, R]`围成的容器的面积可以表示为`area = (R - L) * min(height[L], height[R])`，这个面积公式对后续的推导过程很关键。

![](../../pic/lc-0011-02.png)

我们知道了面积的计算方式，给定数组`height=[1, 8, 6, 2, 5, 4, 8, 3, 7]`，初始化索引`L = 0`，`R = height.size - 1`。接下来就是要通过移动`L`，`R`去寻找区间`[L, R]`围成的最大的面积。那么`L`，`R`要如何移动呢？

假设`L = 0`，`R = 8`，`height[L] < height[R]`。此时`area = (R - L) * min(height[L], height[R]) = (R - L) * height[L] = 8 * 1 = 8`。
1. **如果向左移动`R`**，`R = R - 1 = 7`，`area = (R - L) * min(height[L], height[R]) = (R - L) * height[L]`。
由于索引`R`变小了，`R - L`是一定会变小的，即使`height[R]`变大，`min(height[L], height[R]) = height[L]`，短板依旧是`height[L]`，面积`area = (R - L) * height[L] = 7 * 1 = 7`会变小。所以移动长板对应的索引`R`一定会导致面积`area`变小。
2. **如果向右移动`L`**，`L=L+1=1`，`area = (R - L) * min(height[L], height[R])  =(R - L) * height[R]`。
由于索引`L`变大了，`R - L`是一定会变小的，但是`height[L]`变长了，`min(height[L], height[R]) = height[R]`，短板变成了`height[R]`，`area = (R - L) * height[R] = 7 * 7 = 49`变大了。所以移动短板对应的索引`L`**面积`area`有可能变大**。

总结一下上面的过程，移动长板的索引导致区间`[L, R]`围成的面积`area`一定会变小，移动短板的索引导致区间`[L, R]`围成的面积`area`有可能会变大。我们要找的是最大面积，所以移动长板的索引是没有意义的。这样我们就得到了索引的移动策略：
* 用`max_res`保存最大当前最大面积，每次移动短板对应的索引，计算`area`，更新`max_res`。

## C++代码

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int height_len = height.size();
        int L = 0, R = height_len - 1;
        int max_area = INT_MIN;
        while (L < R) {
           //面积计算公式
            int area = (R - L)*min(height[L], height[R]);
            //更新最大面积
            max_area = max(max_area, area);
            //移动短板对应的索引
            if (height[L] < height[R]) {
                ++L;
            } else {
                --R;
            }
        } 
        return max_area;
    }
};
```
## 复杂度分析

**时间复杂度：** 整个过程只遍历了一遍数组`height`，故时间复杂度为*O(n)*，其中`n`为数组`height`的长度。

**空间复杂度：** 我们只使用了几个整型的变量，故空间复杂度为*O(1)*。

