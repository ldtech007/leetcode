> *题目链接：*  https://leetcode.cn/problems/non-overlapping-intervals/

# LeetCode 435. 无重叠区间

## 题目描述

给定一个区间的集合 `intervals` ，其中 intervals[i] = [start<sub>i</sub>, end<sub>i</sub>] 。返回 *需要移除区间的最小数量，使剩余区间互不重叠* 。

**举个例子：**

```
输入: intervals = [[1,2],[2,3],[3,4],[1,3]]
输出: 1
解释: 移除 [1,3] 后，剩下的区间没有重叠。
```

## 思路解析

首先要明确怎样的区间算是不重叠的，`[1, 2]`和`[2, 3]`这两个区间虽然都包含`2`，但是根据题目的意思他俩是不重叠的。

接下来要制订移除重叠区间的策略。假设区间`a=[1, 3]`，`b=[4, 6]`，`c=[2, 5]`，先对三个区间**以区间起点**为主键进行排序。如下图，相邻的`a`、`c`存在重叠。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0435-01.png)

1. **如果移除区间`a`**，那么`b`和`c`依旧存在重叠，那么还需移除区间`b`和`c`中的一个才能保证剩下的区间不重叠。这个时候总共需要移除`2`次。
2. **如果移除区间`c`**，`a`和`b`不存在重叠。这个时候总共需要移除`1`次。

总结上面的两种情况，我们发现**相邻两个重叠区间中，移除区间终点较大的那个区间**可以保证最终移除的区间数是最少的。

## C++代码

```cpp
class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        int intervals_len = intervals.size();
        if (intervals_len == 0) 
            return 0;
        //interval的第一个元素作为key排序
        sort(intervals.begin(),intervals.end(),[](const vector<int>& interval1,const vector<int>& interval2){
            return interval1[0] < interval2[0];
        });

        int res = 0;
        int tmpEnd = intervals[0][1];
        for (int i = 1; i < intervals_len; ++i) {
            //没有重叠
            if (intervals[i][0] >= tmpEnd) {
                tmpEnd = intervals[i][1];
            //有重叠
            } else {
                //移除重叠区间[start1，end1]和[start2, end2]中end1、end2值较大的那个区间。
                tmpEnd = min(tmpEnd, intervals[i][1]);
                ++res;
            }
        }
        return res;
    }
};
```

## java代码

```java
class Solution {
    public int eraseOverlapIntervals(int[][] intervals) {
        int intervals_len = intervals.length;
        if (intervals_len == 0) {
            return 0;
        }

        // interval的第一个元素作为key排序
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));

        int res = 0;
        int tmpEnd = intervals[0][1];
        for (int i = 1; i < intervals_len; i++) {
            // 没有重叠
            if (intervals[i][0] >= tmpEnd) {
                tmpEnd = intervals[i][1];
            // 有重叠
            } else {
                // 移除重叠区间[start1，end1]和[start2, end2]中end1、end2值较大的那个区间。
                tmpEnd = Math.min(tmpEnd, intervals[i][1]);
                res++;
            }
        }
        return res;
    }
}
```

## python代码

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        intervals_len = len(intervals)
        if intervals_len == 0:
            return 0

        # interval的第一个元素作为key排序
        intervals.sort(key=lambda x: x[0])

        res = 0
        tmpEnd = intervals[0][1]
        for i in range(1, intervals_len):
            # 没有重叠
            if intervals[i][0] >= tmpEnd:
                tmpEnd = intervals[i][1]
            # 有重叠
            else:
                # 移除重叠区间[start1，end1]和[start2, end2]中end1、end2值较大的那个区间。
                tmpEnd = min(tmpEnd, intervals[i][1])
                res += 1

        return res
```

## 复杂度分析

**时间复杂度：** 首先排序的时间复杂度是*O(nlogn)*，遍历一遍数组`intervals`需要*O(n)*，总的时间复杂度是*O(nlogn)*。其中`n`为`intervals`的长度。

**空间复杂度：** 只需要几个整型变量，故时间复杂度为*O(1)*。
