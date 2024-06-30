> *题目链接：* https://leetcode.cn/problems/valid-parentheses/
>
> *视频题解：* https://www.bilibili.com/video/BV1hT421Q7g3/

# LeetCode20.有效括号

在我们刷题的时候，适当的借助现有的数据结构往往会起到事半功倍的效果，今天我们讨论一下栈的使用。栈的特点是先进后出，利用栈的这个特点可以巧妙的解决很多问题，借助下面这道题我们进一步来体会一下栈的妙用。

## 题目描述

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s` ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

**举个例子：**

```
输入：s = "()"
输出：true
```

```
输入：s = "(]"
输出：false
```

## 思路解析

首先要理解题意，什么才是题目中描述的有效括号，比如`s = "({)[]}"`, 如下图，把同类型的括号单独按顺序拆出来，每种类型的括号都是能闭合的，但是`s`并不是合法的。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0020-01.png)

因为最先和`'{'`闭合的是`')'`，违背了 **左括号必须用相同类型的右括号闭合** 这个原则。而且上面的这种拆法本身也违反了 **左括号必须以正确的顺序闭合的原则**。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0020-02.png)

很多同学都因为没有理解透题意面试的时候没有把这道题正确的做出来，非常可惜。

理清题意，只需要借助栈，遍历字符串当元素为`'('`、`'{'`、`'['`就压栈，遇到`')'`、`'}'`、`']'`就和栈顶元素比较，能组成合法括号就弹出栈顶元素，不能组成合法括号就返回`false`，直到遍历完字符串，栈为空就返回`true`。整个匹配过程就像我们平时玩的小游戏消消乐。

举个简单的例子，`s = "({}[])"`，匹配过程如下图：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0020-03.png)

遍历完`s`以后，栈是空的，这就说明字符串`s`是合法的。

## C++代码实现

```cpp
class Solution {
public:
    //判断两个字符能否组成合法的括号
    bool isMatch(char& left, char& right) {
        if (left == '(' && right == ')') {
            return true;
        } else if (left == '{' && right == '}') {
            return true;
        } else if (left == '[' && right == ']') {
            return true;
        } else {
            return false;
        }
    }
    bool isValid(string s) {
        stack<char> st_ch;
        int s_len = s.length();
        //s的长度为奇数,一定是非法的
        if (s_len % 2 != 0) {
            return false;
        } 
        for (auto& ch:s) {
            //遇到'('、'{'、'['就压栈
            if (ch == '(' || ch == '{' || ch == '[')
                st_ch.push(ch);
            //遇到')'、'}'、']'就和栈顶元素比较，能组成合法括号就弹出栈顶元素
            //栈的操作一定要小心，不要对空栈进行pop操作
            else if (!st_ch.empty() && isMatch(st_ch.top(), ch))
                st_ch.pop();
            else 
                return false;
        }
        if (st_ch.empty()) {
            return true;
        } 
        return false;
    }
};

```

## 复杂度分析

**时间复杂度：** 只遍历一遍字符串，故时间复杂度是*O(n)*，`n`为字符串长度。

**空间复杂度：** 我们用到了一个栈`st_ch`，故空间复杂度为*O(n)*，`n`最大为字符串长度。
