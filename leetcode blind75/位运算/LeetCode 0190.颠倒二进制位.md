
> *题目链接：* https://leetcode.cn/problems/reverse-bits/
>
>*视频题解：* https://www.bilibili.com/video/BV1W2421Z7bi/

# LeetCode 190.颠倒二进制位

## 题目描述

颠倒给定的 `32` 位无符号整数的二进制位。

举个例子：

```
输入：n = 00000010100101000001111010011100
输出：964176192 (00111001011110000010100101000000)
解释：输入的二进制串 00000010100101000001111010011100 表示无符号整数 43261596，
     因此返回 964176192，其二进制表示形式为 00111001011110000010100101000000。
```

## 知识回顾

1. 左移运算符 `<<`，可以将一个对象的二进制向左移`n`位，左边`n`位丢弃，右边`n`位补`0`。比如，`a = 1101`。

```
a << 2 = 0100
```

2. 右移运算符 `>>`，可以将一个对象的二进制向右移`n`位，右边`n`位丢弃，左边`n`位补`0`。比如，`a = 1101`。

```
a >> 2 = 0011
```

## 思路解析

### 方法一 按位颠倒

我们先将题目简化一下，对于一个`8`位的二进制`10110111`，如何进行颠倒成`11101101`呢？

先定义一个`8`位无符号的`res = 0`，我们将二进制的第`i`位，放到`res`的第`7 - i`位（位数从`0`开始）就可以完成整个过程的翻转。过程如下图：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0190-01.png)

针对上面的结论稍加修改就可以应用到`32`位无符号整数上面。

先定义一个存放结果的`32`位无符号变量 `res = 0`，循环遍历原串二进制的第`i`位，将其放到`res`的第`31-i`位，遍历完原串即得到最终翻转后的结果`res`。

### C++代码

```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t res = 0;
        for (int i = 0; i < 32; ++i) {
            //原串的第i位
            int bit = n & 1;
            //将原串的第i位放到res的第31-i位
            res |= (bit << (31 - i));
            n >>= 1;
        }
        return res;
    }
};
```

### java代码

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int res = 0;
        for (int i = 0; i < 32; ++i) {
            // 原串的第i位
            int bit = n & 1;
            // 将原串的第i位放到res的第31-i位
            res |= (bit << (31 - i));
            n >>= 1;
        }
        return res;
    }
}
```

### python代码

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        res = 0
        for i in range(32):
            # 原串的第i位
            bit = n & 1
            # 将原串的第i位放到res的第31-i位
            res |= (bit << (31 - i))
            n >>= 1
        return res
```

### 方法二 分治

下面介绍另一种分治方法，对一些刚刷题的同学来说可能比较难理解。

* 把`32`位分成左右两部分`16`位，左右两部分交换。可以通过`n = (n >> 16) | (n << 16)`来做到。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0190-02.png)

* 把`16`位分成左右两部分`8`位，左右两部分交换。可以通过`n = ((n & 0xff00ff00) >> 8) | ((n & 0x00ff00ff) << 8)`来做到。`16`进制`0x00ff00ff`和`32`位二进制`0000 0000 1111 1111 0000 0000 1111 1111`是等价的，`0xff00ff00`和`1111 1111 0000 0000 1111 1111 0000 0000`是等价的。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0190-03.png)

* 把`8`位分成左右两部分`4`位，左右交换。可以通过`n = ((n & 0xf0f0f0f0) >> 4) | ((n & 0x0f0f0f0f) << 4)`来做到。`16`进制`0xf0f0f0f0`和二进制`1111 0000 1111 0000 1111 0000 1111 0000`是等价的，`0x0f0f0f0f`和`0000 1111 0000 1111 0000 1111 0000 1111`是等价的。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0190-04.png)

* 把`4`位分成左右两部分`2`位，左右交换。可以通过`n = ((n & 0xcccccccc) >> 2) | ((n & 0x33333333) << 2)`来做到。`16`进制`0xcccccccc`和二进制`1100 1100 1100 1100 1100 1100 1100 1100`等价，`0x33333333`和二进制`0011 0011 0011 0011 0011 0011 0011 0011`等价。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0190-05.png)

* 把`2`位分成左右两部分`1`位，左右交换。可以通过 `n = ((n & 0xaaaaaaaa) >> 1) | ((n & 0x55555555) << 1)`来实现。其中`16`进制`0xaaaaaaaa`和二进制`1010 1010 1010 1010 1010 1010 1010 1010`等价，`0x55555555`和`0101 0101 0101 0101 0101 0101 0101 0101`等价。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0190-06.png)

### C++代码

```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        //左右16位交换
        n = (n >> 16) | (n << 16);
        //左右8位交换
        n = ((n & 0xff00ff00) >> 8) | ((n & 0x00ff00ff) << 8);
        //左右4位交换
        n = ((n & 0xf0f0f0f0) >> 4) | ((n & 0x0f0f0f0f) << 4);
        左右2位交换
        n = ((n & 0xcccccccc) >> 2) | ((n & 0x33333333) << 2);
        左右1位交换
        n = ((n & 0xaaaaaaaa) >> 1) | ((n & 0x55555555) << 1);
        return n;
    }
};
```

### java代码

```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        // 左右16位交换
        n = ((n >> 16) & 0x0000ffff) | ((n << 16) & 0xffff0000);
        // 左右8位交换
        n = ((n >> 8) & 0x00ff00ff) | ((n << 8) & 0xff00ff00);
        // 左右4位交换
        n = ((n >> 4) & 0x0f0f0f0f) | ((n << 4) & 0xf0f0f0f0);
        // 左右2位交换
        n = ((n >> 2) & 0x33333333) | ((n << 2) & 0xcccccccc);
        // 左右1位交换
        n = ((n >> 1) & 0x55555555) | ((n << 1) & 0xaaaaaaaa);
        return n;
    }
}
```

### python代码

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        # 左右16位交换
        n = (n >> 16) | (n << 16)
        # 左右8位交换
        n = ((n & 0xff00ff00) >> 8) | ((n & 0x00ff00ff) << 8)
        # 左右4位交换
        n = ((n & 0xf0f0f0f0) >> 4) | ((n & 0x0f0f0f0f) << 4)
        # 左右2位交换
        n = ((n & 0xcccccccc) >> 2) | ((n & 0x33333333) << 2)
        # 左右1位交换
        n = ((n & 0xaaaaaaaa) >> 1) | ((n & 0x55555555) << 1)
        return n
```

## 复杂度分析

**时间复杂度：** 两种方法都是 *O(1)*，最多处理`32`位，分治是`log32`位，都是常量时间。

**空间复杂度：** 两种方法都是 *O(1)*，只使用有限个整型变量。
