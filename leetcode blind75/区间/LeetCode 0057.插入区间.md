> *题目链接：* https://leetcode.cn/problems/insert-interval/
>
>*视频题解：* https://www.bilibili.com/video/BV1VHHUezEw7/


# LeetCode 57.插入区间

## 题目描述

给你一个 **无重叠的** ，按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

**举个例子：**

```
输入：intervals = [[1,3],[6,9]], newInterval = [2,5]
输出：[[1,5],[6,9]]
```

## 思路解析

循环遍历数组`intervals`，对于`newInterval`和`intervals[i]`我们分三种情况来讨论：

1. `newInterval`在`intervals[i]`的左边，此时把`newInterval`和`intervals`区间`[i,n)`的元素依次压入`res`中，并返回`res`， 其中`n`为`intervals`的大小，`res`为存储结果的数组。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0057-01.png)

2. `newInterval`在`intervals[i]`的右边，此时把`intervals[i]`压入`res`中，`newInterval`继续跟`intervals[i]`后面的元素比较，`++i`。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0057-02.png)

3. `newInterval`和`intervals[i]`有重叠，更新`newInterval`。`newInterval[0] = min(newInterval[0], intervals[i][0])`，`newInterval[1] = max(newInterval[1], intervals[i][1])`，更新后的`newInterval`继续和`intervals[i]`后面的元素比较，`++i`。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0057-03.png)

## C++代码

```cpp
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {

        vector<vector<int>> res;
        int intervals_len = intervals.size();
        for (int i = 0; i < intervals_len; ++i) {
            //newInterval在intervals[i]的左边
            if (newInterval[1] < intervals[i][0]) {
                //res中保存newInterval
                res.push_back(newInterval);
                for (int j = i; j < intervals_len; ++j) {
                    res.push_back(intervals[j]);
                }
                return res;
            //newInterval在intervals[i]的右边
            } else if (newInterval[0] > intervals[i][1]) {
                //res中保存intervals[i]
                 res.push_back(intervals[i]);
            //newInterval和intervals[i]重叠
            } else {
                //更新newInterval
                newInterval[0] = min(newInterval[0], intervals[i][0]);
                newInterval[1] = max(newInterval[1], intervals[i][1]);
            }

        }
        
        res.push_back(newInterval);
        return res;
    }
};

```

## java代码

```java
class Solution {
    public int[][] insert(int[][] intervals, int[] newInterval) {
        List<int[]> resList = new ArrayList<>();
        int intervals_len = intervals.length;

        for (int i = 0; i < intervals_len; i++) {
            // newInterval在intervals[i]的左边
            if (newInterval[1] < intervals[i][0]) {
                // resList中保存newInterval
                resList.add(newInterval);
                for (int j = i; j < intervals_len; j++) {
                    resList.add(intervals[j]);
                }
                // 将List转换为二维数组返回
                return resList.toArray(new int[resList.size()][]);
            // newInterval在intervals[i]的右边
            } else if (newInterval[0] > intervals[i][1]) {
                // resList中保存intervals[i]
                resList.add(intervals[i]);
            // newInterval和intervals[i]重叠
            } else {
                // 更新newInterval
                newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
                newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            }
        }

        resList.add(newInterval);
        // 将List转换为二维数组返回
        return resList.toArray(new int[resList.size()][]);
    }
}
```

## python代码

```python
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        res = []
        intervals_len = len(intervals)

        for i in range(intervals_len):
            # newInterval在intervals[i]的左边
            if newInterval[1] < intervals[i][0]:
                # res中保存newInterval
                res.append(newInterval)
                for j in range(i, intervals_len):
                    res.append(intervals[j])
                return res
            # newInterval在intervals[i]的右边
            elif newInterval[0] > intervals[i][1]:
                # res中保存intervals[i]
                res.append(intervals[i])
            # newInterval和intervals[i]重叠
            else:
                # 更新newInterval
                newInterval[0] = min(newInterval[0], intervals[i][0])
                newInterval[1] = max(newInterval[1], intervals[i][1])

        res.append(newInterval)
        return res
```

## 复杂度分析

**时间复杂度：** 需要遍历一遍数组`intervals`，所以时间复杂度为*O(n)*，其中`n`为数组的长度。

**空间复杂度：** 需要用到`res`来保存结果，所以空间复杂度为*O(n)*，其中`n`为数组的长度。

