*题目链接：* https://leetcode.cn/problems/top-k-frequent-elements/
>
>*视频题解：* https://www.bilibili.com/video/BV1aNW8esE9V/

# LeetCode 347. 前 K 个高频元素

## 题目描述

给你一个整数数组 `nums` 和一个整数 `k` ，请你返回其中出现频率前 `k`高的元素。你可以按 **任意顺序** 返回答案。

**举个例子：**

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

## 思路解析

### 方法一 hash表+桶排序

定义`hash`表`u_mapCount`统计数组`nums`中每个元素的频率，元素频率作为数组`Freq`的索引，对应频率元素组成的数组作为`Freq`的元素。

比如对于数组`nums = [1,1,1,2,2,3]`获取前`k`个高频元素步骤如下：

1. 先统计其中元素的频率如下图。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0347-01.png)

2. 然后使用桶排序，把元素频率作为数组`Freq`的索引，对应频率元素组成的数组作为`Freq`的元素。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0347-02.png)

3. 最后逆序获取二维数组`Freq`中`k`个元素作为结果返回。


### C++代码

```cpp
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int> res;
        unordered_map<int, int> u_mapCount;
        vector<vector<int>> Freq(nums.size() + 1, vector<int>());
        //统计每个元素的频率
        for (int i = 0; i < nums.size(); ++i) {
            u_mapCount[nums[i]] += 1; 
        }
        //元素的频率作为数组的索引，相同频率元素组成的数组作为数组的元素
        for (auto& pair : u_mapCount) {
            Freq[pair.second].emplace_back(pair.first);
        }
        //逆序获取k个最高频的元素
        for (int i = Freq.size() - 1; i >= 0; --i) {
            for (int j = 0; j < Freq[i].size(); ++j) {
                res.emplace_back(Freq[i][j]);
                if (res.size() == k) {
                    return res;
                }
            }
        }
        return res;
    }
};
```

### java代码

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
         int[] res = new int[k];
        Map<Integer, Integer> u_mapCount = new HashMap<>();
        List<List<Integer>> Freq = new ArrayList<>(nums.length + 1);

        // 初始化每个频率对应的数组
        for (int i = 0; i <= nums.length; ++i) {
            Freq.add(new ArrayList<>());
        }

        // 统计每个元素的频率
        for (int num : nums) {
            u_mapCount.put(num, u_mapCount.getOrDefault(num, 0) + 1);
        }

        // 元素的频率作为数组的索引，相同频率元素组成的数组作为数组的元素
        for (Map.Entry<Integer, Integer> entry : u_mapCount.entrySet()) {
            Freq.get(entry.getValue()).add(entry.getKey());
        }

        // 逆序获取k个最高频的元素
        int index = 0;
        for (int i = Freq.size() - 1; i >= 0; --i) {
            for (int num : Freq.get(i)) {
                res[index++] = num;
                if (index == k) {
                    return res;
                }
            }
        }

        return res;
    }
}
```

### python代码

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        res = []
        u_map_count = defaultdict(int)
        Freq = [[] for _ in range(len(nums) + 1)]
        
        # 统计每个元素的频率
        for num in nums:
            u_map_count[num] += 1
        
        # 元素的频率作为数组的索引，相同频率元素组成的数组作为数组的元素
        for num, freq in u_map_count.items():
            Freq[freq].append(num)
        
        # 逆序获取k个最高频的元素
        for i in range(len(Freq) - 1, -1, -1):
            for num in Freq[i]:
                res.append(num)
                if len(res) == k:
                    return res
        
        return res
```

### 复杂度分析

**时间复杂度：** 构造`hash`表需要遍历一遍数组`nums`，然后构造频率表需要遍历一遍`hash`表，总的时间复杂度为*O(n)*，其中`n`为数组`nums`的长度。

**空间复杂度：** 需要用到一个`hash`表和一个频率表，他们的长度均为`n`，所以空间复杂度为*O(n)*。

### 方法二 使用小根堆

本题也可以利用小根堆的特点来解，c++中可以使用优先队列（可以实现堆的操作）。

定义`hash`表`u_mapCount`统计数组`nums`中每个元素的频率，优先队列`q`用来存放频率最大的`k`个元素。

把`hash`表`u_mapCount`中的对象作为优先队列的元素，这里需要实现一个仿函数`cmp`作为优先队列模板类的参数，用作优先队列元素的排序。

```cpp
class cmp {
public:
     bool operator()(pair<int, int>left, pair<int, int> right) {
         // 表示将u_mapCount中的对象根据频率排序，顺序为递减
         // 用来实现小根堆
         return left.second > right.second;
     }
 };
```

使用优先队列的算法如下：

1. 使用`hash`表`u_mapCount`统计数组`nums`中元素出现的频率。
2. 遍历`hash`表`u_mapCount`，如果优先队列`q`中元素个数小于`k`，将`u_mapCount`中的元素入队，否则，如果优先队列`q`中元素个数等于`k`且`u_mapCount`中元素大于`q.top()`, 就用`u_mapCount`中元素替换`q.top()`。
3. 返回`nums`在优先队列中存在的元素。

### C++代码

```cpp
class cmp {
public:
     bool operator()(pair<int, int>left, pair<int, int> right) {
         // 表示将u_mapCount中的对象根据频率排序，顺序为递减
         return left.second > right.second;
     }
 };

class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int> res;
        unordered_map<int, int> u_mapCount;
        vector<vector<int>> Freq(nums.size() + 1, vector<int>());
        for (int i = 0; i < nums.size(); ++i) {
            u_mapCount[nums[i]] += 1; 
        }
        //小根堆，堆中元素是pair类型的，pair为上面hash表u_mapCount中的一个对象
        priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> q;
        for (auto& item : u_mapCount) {
            if (q.size() < k) {
                q.emplace(item);
            } else {
                //item的频率比小根堆中最小的值大，则替换它
                //保持小根堆中不超过k个元素
                if (q.top().second < item.second) {
                    q.pop();
                    q.emplace(item);
                } 
            }
        }
        while(!q.empty()) {
            res.emplace_back(q.top().first);
            q.pop();
        }
        return res;
    }
};
```

### java代码

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        int[] res = new int[k];
        Map<Integer, Integer> u_mapCount = new HashMap<>();
        
        // 统计每个元素的频率
        for (int num : nums) {
            u_mapCount.put(num, u_mapCount.getOrDefault(num, 0) + 1);
        }

        // 小根堆，堆中元素是Map.Entry类型的，Entry为上面hash表u_mapCount中的一个对象
        PriorityQueue<Map.Entry<Integer, Integer>> q = new PriorityQueue<>(k, new Comparator<Map.Entry<Integer, Integer>>() {
            public int compare(Map.Entry<Integer, Integer> a, Map.Entry<Integer, Integer> b) {
                return a.getValue() - b.getValue();  // 比较频率，构造小根堆
            }
        });

        for (Map.Entry<Integer, Integer> item : u_mapCount.entrySet()) {
            if (q.size() < k) {
                q.offer(item);
            } else {
                // item的频率比小根堆中最小的值大，则替换它，保持小根堆中不超过k个元素
                if (q.peek().getValue() < item.getValue()) {
                    q.poll();
                    q.offer(item);
                }
            }
        }

        // 从小根堆中获取结果
        int index = 0;
        while (!q.isEmpty()) {
            res[index++] = q.poll().getKey();
        }

        return res;

    }
}
```

### python代码

```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        res = []
        u_map_count = defaultdict(int)
        
        # 统计每个元素的频率
        for num in nums:
            u_map_count[num] += 1

        # 小根堆，堆中元素是元组 (频率, 元素)，使用 heapq 实现
        q = []

        for num, freq in u_map_count.items():
            if len(q) < k:
                heapq.heappush(q, (freq, num))
            else:
                # item的频率比小根堆中最小的值大，则替换它，保持小根堆中不超过k个元素
                if q[0][0] < freq:
                    heapq.heappop(q)
                    heapq.heappush(q, (freq, num))

        # 从小根堆中获取结果
        while q:
            res.append(heapq.heappop(q)[1])

        return res
```

### 复杂度分析

**时间复杂度：** 构造`hash`表需要遍历一遍数组`nums`，优先队列插入的时间复杂度为*O(logk)*，总的时间复杂度为*O(nlogk)*，其中`n`为数组`nums`的长度，`k`为优先队列中元素的个数。

**空间复杂度：** 需要用到一个`hash`表和一个优先队列，他们的长度分别为`n`和`k`，其中`k < n`，所以空间复杂度为*O(n)*。

