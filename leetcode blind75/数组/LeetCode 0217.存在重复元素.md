> *题目链接：* https://leetcode.cn/problems/contains-duplicate/

> *视频题解：* https://b23.tv/OJoSzyx

# LeetCode 217. 存在重复元素

## 题目描述

给你一个整数数组 `nums` 。如果任一值在数组中出现 **至少两次** ，返回 `true`；如果数组中每个元素互不相同，返回 `false`。

**举个例子：**

```
输入： nums = [1,2,3,1]
输出： true
```

## 思路解析

 本题可以通过暴力两两对比的方式来解，但是时间复杂度太高*O(n<sup>2</sup>)*，oj会判定超时。下面我来介绍两种时间复杂度更低的解法。

### 方法一 排序法

首先对给定的`nums`进行排序，然后遍历排序后的`nums`进行遍历，如果存在`nums[i] == nums[i-1]`，说明`nums`中存在重复的元素。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0217-01.png)

### C++代码

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
       //先排序
       sort(nums.begin(), nums.end());
       int nums_len = nums.size();
       //遍历排序后的nums
       for (int i = 1; i < nums_len; ++i) {
           //nums中存在重复的元素
           if (nums[i] == nums[i-1]) {
               return true;
           } 
       }
       return false;
    }
};
```

### java代码

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        // 先排序
        Arrays.sort(nums);
        int numsLen = nums.length;
        // 遍历排序后的nums
        for (int i = 1; i < numsLen; i++) {
            // nums中存在重复的元素
            if (nums[i] == nums[i-1]) {
                return true;
            }
        }
        return false;
    }
}
```

### python代码

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        # 先排序
        nums.sort()
        nums_len = len(nums)
        # 遍历排序后的nums
        for i in range(1, nums_len):
            # nums中存在重复的元素
            if nums[i] == nums[i-1]:
                return True
        return False
```

### 复杂度分析

**时间复杂度：** 排序最快的时间复杂度是*O(nlogn)*，遍历一遍`nums`的时间复杂度是*O(n)*，所以总的时间复杂度是*O(nlogn) + O(n) = O(nlogn)*，其中`n`为`nums`的长度。

**空间复杂度：** 排序的空间复杂度是*O(logn)*，算法本身的空间复杂度是*O(1)*，所以总的空间复杂度是*O(logn)*。


### 方法二 哈希表

定义一个哈希表`hash_map`用来保存已经遍历过的元素，遍历`nums`，如果`nums[i]`在`hash_map`中，说明存在重复的元素。否则把`nums[i]`作为`key`，`1`作为`value`存到`hash_map`中。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0217-02.png)

### C++代码

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        //unordered_map底层是hash表实现的
        unordered_map<int, int> u_map;
        int nums_len = nums.size();
        for (int i = 0; i < nums_len; ++i) {
            //hash表中已经存在nums说明有重复的元素
            if (u_map.count(nums[i])) {
                return true;
            } 
            //hash表中不存在nums[i]，把nums[i]存到hash表中
            u_map[nums[i]] = 1;
        }
        return false;
    }
};
```

### java代码

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashMap<Integer, Integer> u_map = new HashMap<>();
        for (int num : nums) {
            // hash表中已经存在num，说明有重复的元素
            if (u_map.containsKey(num)) {
                return true;
            }
            // hash表中不存在num，把num存到hash表中
            u_map.put(num, 1);
        }
        return false;
    }
}
```

### python代码

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        u_map = {}
        for num in nums:
            # hash表中已经存在num，说明有重复的元素
            if num in u_map:
                return True
            # hash表中不存在num，把num存到hash表中
            u_map[num] = 1
        return False
```

### 复杂度分析

**时间复杂度：** `hash`表查找和插入的时间都是*O(1)*的，遍历`nums`的时间是*O(n)*，所以总的时间复杂度是*O(n)*，`n`为`nums`的长度。

**空间复杂度：** 整个过程需要借助一个`hash`表来存储已经遍历过的元素，所以空间复杂度为*O(n)*，其中`n`为`nums`的长度。
