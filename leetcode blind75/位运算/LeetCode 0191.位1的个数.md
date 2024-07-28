
> *题目链接：* https://leetcode.cn/problems/number-of-1-bits/
>
>*视频题解：* https://www.bilibili.com/video/BV1ir421M7XU/

# LeetCode 191.位1的个数

## 题目描述

编写一个函数，输入是一个无符号整数 **（以二进制串的形式）**，返回其二进制表达式中数字位数为 `1` 的个数（也被称为汉明重量）。

## 思路解析

### 方法一

定义`res`保存`1`的个数。对于无符号整数`n`，统计其中`1`的个数步骤如下：
1. 如果`n != 0`，`n`对应二进制的最右边一位如果是`1`，`res++`，进入`步骤2`。否则，返回`res`。
2. 把`n`对应的二进制向右移动一位，进入`步骤1`。

关键在于如何**获取`n`对应二进制的最右边一位**和怎样**将`n`对应的二进制向右移一位**。

**获取n对应二进制最右面一位有两种方式：**
1. `n`和`1`进行与运算`n & 1`可以获取`n`对应二进制的最右面一位。
2. `n`对`2`取余`n % 2`即可获取`n`对应二进制的最右面一位。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0191-01.png)

**将`n`对应的二进制右移一位有两种方式：**
1. 直接使用编程语言自带的右移符号，比如`c++`可以写为`n >> 1`。
2. `n / 2` 也可以将`n`对应的二进制右移一位。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0191-02.png)

### C++代码

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int res = 0;
        while(n) {
           //获取n对应二进制最右边一位
           if (n % 2) {
               ++res;
           }
           //n对应的二进制右移一位
           n = n / 2;
        }
        return res;
    } 
};
```

### java代码

```java
class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        while (n != 0) {
            // 获取n对应二进制最右边一位
            if (n % 2 == 1) {
                res++;
            }
            // n对应的二进制右移一位
            n = n / 2;
        }
        return res;
    }
}
```

### python代码

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        res = 0
        while n:
            # 获取n对应二进制最右边一位
            if n % 2:
                res += 1
            # n对应的二进制右移一位
            n = n // 2
        return res
```

### 方法二

定义`res`保存`1`的个数。对于无符号整数`n`，统计其中`1`的个数步骤如下：
1. 如果`n != 0`，`res++`，进入`步骤2`。否则，返回`res`。
2. `n = n & (n - 1)`，进入`步骤1`。

`n & (n - 1)`其实就是将`n`对应的二进制最右边值为`1`的`bit位`置为`0`。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0191-03.png)

在纸上继续按照上面步骤模拟一遍，会帮助大家更好的理解。

### C++代码

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int res = 0;
        while(n) {
            ++res;
            //n对应二进制最右边不为零的bit位置为零
            n = n&(n-1);
        }
        return res;
    } 
};
```

### java代码

```java
class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        while (n != 0) {
            res++;
            // n对应二进制最右边不为零的bit位置为零
            n = n & (n - 1);
        }
        return res;
    }
}
```

### python代码

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        res = 0
        while n:
            res += 1
            # n对应二进制最右边不为零的bit位置为零
            n = n & (n - 1)
        return res
```

## 复杂度分析

**时间复杂度：** 两种方法都是*O(1)*，因为二进制最长为`32`位。

**空间复杂度：** 两种方法都是*O(1)*，只使用了几个整型变量。


