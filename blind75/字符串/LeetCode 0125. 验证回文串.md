> *题目链接：* https://leetcode.cn/problems/valid-palindrome/

# LeetCode 125. 验证回文串

## 题目描述

如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个 **回文串** 。

字母和数字都属于字母数字字符。

给你一个字符串 `s`，如果它是 **回文串** ，返回 `true` ；否则，返回 `false` 。

**举个例子：**

```
输入: s = "A man, a plan, a canal: Panama"
输出：true
解释："amanaplanacanalpanama" 是回文串。
```

## 思路解析

先明确回文串的概念：**正着读和反着读都一样**。换句话说就是对于字符串`s`，将`s`进行反转得到字符串`s'`，如果`s == s'`，那么`s`就是**回文串**。

本题中的字符串`s`包含一些非字母数字干扰字符，在判断的时候可以跳过干扰字符。

采用双指针法，定义两个变量`left = 0`，`right = s.length() - 1`。
1. 当`left < right`，`left`向右移动，直到`left`指向字母或数字，`right`向左移动，直到`right`指向字母或数字。如果`left >= right`说明`s`是回文串，否则进入`步骤2`。
2. 比较`left`和`right`指向字符对应的小写字符是否相等，如果不相等说明`s`不是回文串，否则`left`和`right`同时移动，`++left`，`--right`，进入`步骤1`。

## C++代码

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        //定义两个索引
        int left = 0, right = s.length() - 1;
        while (left < right) {
            //left需要指在字母或数字上
            while (left < right && !isalnum(s[left])) {
                ++left;
            }
            //right需要指在字母或数字上
            while (left < right && !isalnum(s[right])) {
                --right;
            }

            if (tolower(s[left]) != tolower(s[right])) {
                return false;
            }
            ++left;
            --right;
        }
        return true;
    }

};
```

## 复杂度分析

**时间复杂度：** 只需要遍历一遍字符串，所以时间复杂度为*O(n)*，其中`n`为字符串的长度。

**空间复杂度：** 只使用了两个索引，所以空间复杂度为*O(1)*。
