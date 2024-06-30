> *题目链接：* https://leetcode.cn/problems/longest-repeating-character-replacement/
>
> *视频题解：* https://www.bilibili.com/video/BV1zf421d7Ui/

# LeetCode 424. 替换后的最长重复字符

## 题目描述

给你一个字符串 `s` 和一个整数 `k` 。你可以选择字符串中的任一字符，并将其更改为任何其他大写英文字符。该操作最多可执行 `k` 次。

在执行上述操作后，返回包含相同字母的最长子字符串的长度。

**举个例子：**

```cpp
输入：s = "AABABBA", k = 1
输出：4
解释：
将中间的一个'A'替换为'B',字符串变为 "AABBBBA"。
子串 "BBBB" 有最长重复字母, 答案为 4。
可能存在其他的方法来得到同样的结果。
```

## 思路解析

本题是一道比较经典的滑动窗口问题，目标是在字符串`s`中**找到最长的一个窗口**，满足**最多替换`k`个字母，窗口就可以变成所有字母都是相同的**，这个窗口的长度就是我们要找的答案。

定义`left = 0`，`right = 0`来表示窗口的左右边界，`res`来保存结果，哈希表`u_charCount`保存当前窗口中所有字母出现的频率。窗口的长度为`right - left + 1`，窗口中字母出现的最高频率为`maxCount`。所以`right - left + 1 - maxCount`就是当前窗口最多要替换的字母数，`right - left + 1 - maxCount`小于等于`k`时`[left, right]`就是我们要找的一个候选窗口。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0424-01.png)

上图中，`[left,right]`组成的窗口中出现频率最高的字母为`A`，所以此时`maxCount = 3`，只需要把`B`替换成`A`就可以满足窗口就中所有字母都是相同的。

问题的关键是左右边界如何滑动，窗口滑动策略如下：

1. `right - left + 1 - maxCount <= k`时找到以当前`right`为右边界的候选窗口，更新结果`res = max(res, right - left + 1)`，`right`向右滑动并更新`hash`表`u_charCount[s[right]]++`和当前窗口中字母的最高频率`maxCount = max(maxCount, u_charCount[s[right]])`，直到`right - left + 1 - maxCount > k`。
2. `right - left + 1 - maxCount > k`时未找到以当前`right`为右边界候选窗口，`left`向右滑动，更新`hash`表`u_charCount[s[left]]--`，直到`right - left + 1 - maxCount <= k`。

根据上面的策略我们可以获得以`s`任意位置为右边界(枚举右边界)的所有候选窗口，只需要把其中最长的一个窗口的长度返回即可。

有些同学会有疑问，为啥`left`向右滑动时只更新了`u_charCount`，却没有更新`maxCount`？

这是因为`left`向右滑动时不会出现比当前`res`更长的候选窗口，并且此时`maxCount`不会增大。想获得比当前`res`更长的候选窗口，必须保证`right`和`maxCount`都是相比之前变大的，所以只有`right`右滑的时候才会有更长的候选窗口出现。`left`右滑的时候不会涉及到`res`的更新，即使`maxCount`未更新导致候选窗口没有严格的满足条件`right - left + 1 - maxCount <= k`，也是不影响结果`res`的，所以没必要更新`maxCount`。当然想更新也是可以的，多几行代码，只是没有必要。

## C++代码

```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        //统计滑动窗口中每个字符出现的次数
        unordered_map<char, int> u_charCount;
        int res = 0;
        int left = 0, right = 0;
        //维护滑动窗口中字符出现的最大次数
        int maxCount = 0;
        int s_len = s.length();
        while (right < s_len) {
            u_charCount[s[right]] += 1;
            maxCount = max(maxCount, u_charCount[s[right]]);
            //当最少的操作数大于k，改变左边界，寻找更优的窗口
            while (right - left + 1 - maxCount > k) {
                u_charCount[s[left]] -= 1;
                ++left;
            }
            //更新满足条件的最大窗口
            res = max(res, right - left + 1);
            ++right;
        }
        return res;
    }
};
```

## 复杂度分析

**时间复杂度：** `left`和`right`只需要遍历一遍字符串，对`u_charCount`的操作都是常量时间复杂度，所以整体的时间复杂度是*O(n)*，其中`n`是字符串的长度。

**空间复杂度：** 需要使用一个`hash`表，所以空间复杂度是*O(26)*。
