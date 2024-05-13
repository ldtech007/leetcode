> *题目链接：* https://leetcode.cn/problems/valid-anagram/

# LeetCode 242. 有效的字母异位词

## 题目描述

给定两个字符串 `s` 和 `t` ，编写一个函数来判断 `t` 是否是 `s `的字母异位词。

**注意：** 若 `s` 和 `t` 中每个字符出现的次数都相同，则称 `s` 和 `t` 互为字母异位词。

**举个例子：**

```
输入: s = "anagram", t = "nagaram"
输出: true
```

## 思路解析

### 方法一 排序

分别对`s`和`t`进行排序，然后比较排序后的字符串是否相等。

### C++代码

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        return s == t;
    }
};

```

### 复杂度分析

**时间复杂度：** 快排的时间复杂度是*O(nlogn)*，其中`n`是字符串的长度。

**空间复杂度：** 快排的空间复杂度是*O(logn)*。

### 方法二 hash表

假设`s = "anagram"`， `t = "nagaram"`。

1. 定义两个`hash`表`u_mapS`用来统计`s`中字符出现的次数，`u_mapT`用来统计`t`中字符出现的次数。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0242-01.png)

2. 比较两个`hash`表中的元素是否相等。

### C++代码

```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        int s_len = s.length();
        int t_len = t.length();
        if (s_len != t_len)
            return false;
        //保存s中字符出现的次数
        unordered_map<char, int> u_mapS;
        //保存t中字符出现的次数
        unordered_map<char, int> u_mapT;
        for (int i = 0; i < s_len; ++i) {
            u_mapS[s[i]]++;
            u_mapT[t[i]]++;
        }

        for (int i = 0; i < s_len; ++i) {
            if (u_mapS[s[i]] != u_mapT[s[i]])
                return false;
        }
        return true;
    }
};
```

### 复杂度分析

**时间复杂度：** 只需要遍历两遍字符串，哈希表的存取都是常量的时间复杂度，所以时间复杂度为*O(n)*，其中`n`是字符串的长度。

**空间复杂度：** 需要使用两个`hash`表，所以空间复杂度为*O(n)*，其中`n`是字符串的长度。
