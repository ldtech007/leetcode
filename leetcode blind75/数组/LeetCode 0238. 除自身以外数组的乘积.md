> *题目链接：* https://leetcode.cn/problems/product-of-array-except-self/

> *视频题解：* https://b23.tv/GEt0p50

# LeetCode 238. 除自身以外数组的乘积

## 题目描述

给你一个整数数组 `nums`，返回 **数组** `answer` ，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积 。

题目数据 **保证** 数组 `nums` 之中任意元素的全部前缀元素和后缀的乘积都在  **32 位** 整数范围内。

请不要使用除法，且在 *O(n)* 时间复杂度内完成此题。

```
举个例子：
输入: nums = [1,2,3,4]
输出: [24,12,8,6]
```

## 思路解析

题目要求不能使用除法，时间复杂度为*O(n)*，其中`n`为`nums`的长度。

首先引入两个概念，**前缀积**和**后缀积**。

前缀积：数组中第`i`个元素的前缀积为第`i`个元素之前所有元素的乘积。
后缀积：数组中第`i`个元素的后缀积为第`i`个元素之后所有元素的乘积。

我们利用两个跟`nums`数组**长度相同**的数组，`prefix`用来存储`nums`的前缀积，`postfix`用来存储`nums`的后缀积。

```cpp
prefix[0] = 1
prefix[i] = nums[0] * ... * nums[i-1], 0 < i < n

postfix[n-1] = 1
postfix[i] = nums[i+1]  *... * nums[n-1], 0 <= i < n-1 
```

数组`res`用来存储结果。

```cpp
res[i] = prefix[i] * postfix[i], 0 <= i < n 
```

对于数组`nums = [1,2,3,4]`，其计算流程如下：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0238-01.png)

最终结果为`res=[24,12,8,6]`。

## C++代码

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int nums_len = nums.size();
        vector<int> prefix(nums_len, 1);
        vector<int> postfix(nums_len, 1);
        vector<int> res(nums_len, 1);

        //预生成前缀积数组
        prefix[0] = 1;
        for (int i = 1; i < nums_len; ++i) {
            prefix[i] = prefix[i-1] * nums[i-1];
        }
        //预生成后缀积数组
        postfix[nums_len - 1] = 1;
        for (int j = nums_len - 2; j >= 0; --j) {
            postfix[j] = postfix[j+1] * nums[j+1];
        }
        //生成结果
        for (int k = 0; k < nums_len; ++k) {
            res[k] = prefix[k] * postfix[k];
        }
        return res;
    }
};
```

## java代码

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int numsLen = nums.length;
        int[] prefix = new int[numsLen];
        int[] postfix = new int[numsLen];
        int[] res = new int[numsLen];

        // 预生成前缀积数组
        prefix[0] = 1;
        for (int i = 1; i < numsLen; ++i) {
            prefix[i] = prefix[i-1] * nums[i-1];
        }

        // 预生成后缀积数组
        postfix[numsLen - 1] = 1;
        for (int j = numsLen - 2; j >= 0; --j) {
            postfix[j] = postfix[j+1] * nums[j+1];
        }

        // 生成结果
        for (int k = 0; k < numsLen; ++k) {
            res[k] = prefix[k] * postfix[k];
        }

        return res;
    }
}
```

## python代码

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        nums_len = len(nums)
        prefix = [1] * nums_len
        postfix = [1] * nums_len
        res = [1] * nums_len

        # 预生成前缀积数组
        prefix[0] = 1
        for i in range(1, nums_len):
            prefix[i] = prefix[i-1] * nums[i-1]

        # 预生成后缀积数组
        postfix[nums_len - 1] = 1
        for j in range(nums_len - 2, -1, -1):
            postfix[j] = postfix[j+1] * nums[j+1]

        # 生成结果
        for k in range(nums_len):
            res[k] = prefix[k] * postfix[k]

        return res
```

## 代码优化

上面代码我们使用了三个数组`prefix`、`postfix`、`res`，造成了很大浪费。 其实只需要一个`res`就够了，可以使用**一个`int`型的变量代替数组**，中间结果先存在`res`里面。

```cpp
prefix[i] = prefix[i-1] * nums[i-1] => prefix = prefix * nums[i-1]

postfix[j] = postfix[j+1] * nums[j+1] => postfix = postfix * nums[j+1]
```

## 优化后的C++代码

```cpp
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int nums_len = nums.size();
        vector<int> res(nums_len, 1);
        int prefix = 1;
        for (int i = 0; i < nums_len; ++i) {
            //正向遍历保存prefix结果到res
            res[i] = prefix;
            prefix = prefix * nums[i];
        }
        int postfix = 1;
        for (int j = nums_len - 1; j >= 0; --j) {
           //逆向遍历 中间结果（前缀积）乘以后缀积
            res[j] = res[j] * postfix;
            postfix = postfix * nums[j];
        }

        return res;
    }
};
```

## 优化后的java代码

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int numsLen = nums.length;
        int[] res = new int[numsLen];
        int prefix = 1;
        for (int i = 0; i < numsLen; ++i) {
            // 正向遍历保存prefix结果到res
            res[i] = prefix;
            prefix *= nums[i];
        }
        int postfix = 1;
        for (int j = numsLen - 1; j >= 0; --j) {
            // 逆向遍历 中间结果（前缀积）乘以后缀积
            res[j] *= postfix;
            postfix *= nums[j];
        }
        return res;
    }
}
```

## 优化后的python代码

```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        nums_len = len(nums)
        res = [1] * nums_len
        prefix = 1
        for i in range(nums_len):
            # 正向遍历保存prefix结果到res
            res[i] = prefix
            prefix *= nums[i]
        postfix = 1
        for j in range(nums_len - 1, -1, -1):
            # 逆向遍历 中间结果（前缀积）乘以后缀积
            res[j] *= postfix
            postfix *= nums[j]
        return res
```

## 复杂度分析

**时间复杂度：** 只需要遍历一遍`nums`，故时间复杂度是*O(n)*，其中`n`为`nums`的长度。

**空间复杂度：** 使用了一个`res`数组，故空间复杂度是*O(n)*，其中`n`为`nums`的长度。
