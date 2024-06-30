> *题目链接：* https://leetcode.cn/problems/longest-palindromic-substring/
>
> *视频题解：* https://www.bilibili.com/video/BV1w7421Z798/

# LeetCode 5. 最长回文子串

## 题目描述

给你一个字符串 `s`，找到 `s` 中最长的回文子串。

如果字符串的反序与原始字符串相同，则该字符串称为回文字符串。

**举个例子：**

```
输入：s = "babad"
输出："bab"
解释："aba" 同样是符合题意的答案。
```

## 思路解析

首先我们可以通过暴力遍历的方式枚举字符串`s`所有的子串，从中寻找最长的回文串，但是时间复杂度太高，这里不再详细的讲解。下面介绍一种中心扩散法。

分两情况来考虑：

1. **最长回文子串的长度为奇数**，这时我们就枚举最长回文子串所有可能的中心`s[i]`，`0 <= i < s.length`，以中心`s[i]`向两边扩散寻找最长回文子串。如下图`a`就是回文串`"cbabc"`的中心。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0005-01.png)

2. **最长回文子串的长度为偶数**，这时我们就枚举最长回文子串所有可能的中心`s[i]s[i+1]`，`0 <= i < s.length - 1`，以中心`s[i]s[i+1]`向两边扩散寻找最长回文子串。如下图`aa`就是回文串`"cbaabc"`的中心。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0005-02.png)


所以本题的关键是**枚举回文子串的中心**。

## C++代码

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int res_len = 0;
        int res_start = 0;
        int s_len = s.length();
        for (int i = 0; i < s_len; ++i) {
            //若回文串长度为奇数,以s[i]为中心向两边扩散寻找以s[i]为中心的最长回文子串
            int left = i, right = i;
            while (left >= 0 && right < s_len && s[left] == s[right]) {
                if (right - left + 1 > res_len) {
                    res_len = right - left + 1;
                    res_start = left;
                }
                --left;
                ++right;
            }
            //若回文串长度为偶数，以s[i]s[i+1]为中心向两边扩散寻找以s[i]s[i+1]为中心的最长回文子串
            left = i;
            right = i + 1;
            while (left >= 0 && right < s_len && s[left] == s[right]) {
                if (right - left + 1 > res_len) {
                    res_len = right - left + 1;
                    res_start = left;
                }
                --left;
                ++right;
            }
        }
        return s.substr(res_start, res_len);
    }
};
```

## 复杂度分析

**时间复杂度：** 整个过程会遍历`s`中每个元素为中心的最长回文子串，所以时间复杂度是*O(n<sup>2</sup>)*，其中`n`是字符串`s`的长度。

**空间复杂度：** 整个过程只用到几个整型变量，如果算上最终返回的子串占用的空间那么空间复杂度为*O(n)*，其中`n`是字符串`s`的长度。

