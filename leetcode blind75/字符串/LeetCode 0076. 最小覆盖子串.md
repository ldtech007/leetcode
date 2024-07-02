> *题目链接：* https://leetcode.cn/problems/minimum-window-substring/
>
> *视频题解：* https://www.bilibili.com/video/BV1sJ4m1g727/

# LeetCode 76. 最小覆盖子串

## 题目描述

给你一个字符串 `s` 、一个字符串 `t` 。返回 `s` 中涵盖 `t` 所有字符的最小子串。如果 `s` 中不存在涵盖 `t` 所有字符的子串，则返回空字符串 `""` 。

**注意：**

* 对于 `t` 中重复字符，我们寻找的子字符串中该字符数量必须不少于 `t` 中该字符数量。
* 如果 `s` 中存在这样的子串，我们保证它是唯一的答案。

**举个例子：**

```
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

## 思路解析

本题是经典的滑动窗口类型，解决这类问题的关键是窗口左右边界的滑动策略。

定义`hash`表`u_mapT`保存字符串`t`中字符的频率，`hash`表`u_mapWindow`保存滑动窗口中字符出现的次数，`left`保存窗口的左边界，`right`保存窗口的右边界，`resStart`保存最小候选窗口的起点，`resLen`保存最小候选窗口长度。

窗口滑动策略如下：

1. `u_mapT`中的元素不全在`u_mapWindow`中，`right`向右滑动，并更新`u_mapWindow[s[right]]++`，直到`u_mapT`中的元素全在`u_mapWindow`中。
2. `u_mapT`中的元素全在`u_mapWindow`中，`left`向右滑，并更新`u_mapWindow[s[left]]--`、最小候选窗口的起点`resStart`和最小候选窗口长度`resLen`，直到`u_mapT`中的元素不全在`u_mapWindow`中。

根据上面的策略**我们可以获得以`s`任意位置为左边界(枚举左边界)的所有候选窗口**，只需要把其中最短的一个窗口返回即可。

这里在判断`u_mapT`中的元素是否完全在`u_mapWindow`中并没有采用遍历`u_mapT`对其中的元素一个个去`u_mapWindow`中比较。而是借用了一个整型变量`tInWindow`在窗口滑动的过程中就对窗口中字符情况进行统计，大大节省了每次判断都要去遍历`hash`表的时间。

## C++代码

```cpp
class Solution {
public:
    string minWindow(string s, string t) {
        int s_len = s.length();
        int t_len = t.length();
        if (s_len == 0 || t_len == 0) {
            return "";
        }
        //保存t中字符出现的次数
        unordered_map<char, int> u_mapT;
        //保存window中的字符出现的次数
        unordered_map<char, int> u_mapWindow;
        //统计t中的字符
        for (int i = 0; i < t_len; ++i) {
            u_mapT[t[i]]++;
        }
        //t中不重复字符的个数
        int tCount = u_mapT.size();
        //window完全包含t中不重复字符的数量
        int tInWindow = 0;
        int resStart = 0, resLen = INT_MAX;
        int left = 0, right = 0;
        while (right < s_len) {
            u_mapWindow[s[right]]++;
            //假设字符a在t和window中出现的次数相等，就增加tInWindow的计数
            if (u_mapT.count(s[right]) && u_mapWindow[s[right]] == u_mapT[s[right]]) {
                ++tInWindow;
            } 

            while (tInWindow == tCount) {
                //窗口的大小小于已保存的最小长度，更新最小长度
                if (right - left + 1 < resLen) {
                    resStart = left;
                    resLen = right - left + 1; 
                }
                //右移窗口左边界，缩小窗口    
                u_mapWindow[s[left]]--;
                if (u_mapT.count(s[left]) && u_mapT[s[left]] > u_mapWindow[s[left]]) {
                    --tInWindow;
                }
                ++left;
            }
            ++right;
        } 

        if (resLen == INT_MAX) return "";
        return s.substr(resStart, resLen);
    }
};
```

## java代码

```java
class Solution {
    public String minWindow(String s, String t) {
        int s_len = s.length();
        int t_len = t.length();
        if (s_len == 0 || t_len == 0) {
            return "";
        }
        //保存t中字符出现的次数
        Map<Character, Integer> u_mapT = new HashMap<>();
        //保存window中的字符出现的次数
        Map<Character, Integer> u_mapWindow = new HashMap<>();
        //统计t中的字符
        for (int i = 0; i < t_len; ++i) {
            u_mapT.put(t.charAt(i), u_mapT.getOrDefault(t.charAt(i), 0) + 1);
        }
        //t中不重复字符的个数
        int tCount = u_mapT.size();
        //window完全包含t中不重复字符的数量
        int tInWindow = 0;
        int resStart = 0, resLen = Integer.MAX_VALUE;
        int left = 0, right = 0;
        while (right < s_len) {
            u_mapWindow.put(s.charAt(right), u_mapWindow.getOrDefault(s.charAt(right), 0) + 1);
            //假设字符a在t和window中出现的次数相等，就增加tInWindow的计数
            if (u_mapT.containsKey(s.charAt(right)) && u_mapWindow.get(s.charAt(right)).equals(u_mapT.get(s.charAt(right)))) {
                ++tInWindow;
            }

            while (tInWindow == tCount) {
                //窗口的大小小于已保存的最小长度，更新最小长度
                if (right - left + 1 < resLen) {
                    resStart = left;
                    resLen = right - left + 1;
                }
                //右移窗口左边界，缩小窗口
                u_mapWindow.put(s.charAt(left), u_mapWindow.get(s.charAt(left)) - 1);
                if (u_mapT.containsKey(s.charAt(left)) && u_mapWindow.get(s.charAt(left)) < u_mapT.get(s.charAt(left))) {
                    --tInWindow;
                }
                ++left;
            }
            ++right;
        }

        if (resLen == Integer.MAX_VALUE) return "";
        return s.substring(resStart, resStart + resLen);
    }
}
```

## python代码

 ```python
 class Solution:
    def minWindow(self, s: str, t: str) -> str:
        s_len = len(s)
        t_len = len(t)
        if s_len == 0 or t_len == 0:
            return ""
        #保存t中字符出现的次数
        u_mapT = defaultdict(int)
        #保存window中的字符出现的次数
        u_mapWindow = defaultdict(int)
        #统计t中的字符
        for char in t:
            u_mapT[char] += 1
        #t中不重复字符的个数
        tCount = len(u_mapT)
        #window完全包含t中不重复字符的数量
        tInWindow = 0
        resStart, resLen = 0, float('inf')
        left, right = 0, 0
        while right < s_len:
            u_mapWindow[s[right]] += 1
            #假设字符a在t和window中出现的次数相等，就增加tInWindow的计数
            if s[right] in u_mapT and u_mapWindow[s[right]] == u_mapT[s[right]]:
                tInWindow += 1

            while tInWindow == tCount:
                #窗口的大小小于已保存的最小长度，更新最小长度
                if right - left + 1 < resLen:
                    resStart = left
                    resLen = right - left + 1
                #右移窗口左边界，缩小窗口
                u_mapWindow[s[left]] -= 1
                if s[left] in u_mapT and u_mapWindow[s[left]] < u_mapT[s[left]]:
                    tInWindow -= 1
                left += 1
            right += 1

        if resLen == float('inf'):
            return ""
        return s[resStart:resStart + resLen]
 ```

## 复杂度分析

**时间复杂度：** 因为使用变量`tInWindow`提前预先保存了`window`中是否包含`t`的字符，并且只需要遍历一遍`s`和`t`，所以时间复杂度为*O(m + n)*，其中`m`是`s`的长度，`n`是`t`的长度。

**空间复杂度：** 使用了两个`hash`表，具体的复杂度和字符集的大小有关。
