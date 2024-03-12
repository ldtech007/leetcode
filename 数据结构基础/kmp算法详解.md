# kmp算法详解

`kmp`算法加快了字符串的匹配速度但也比较难理解，废话不多说，本文将带你深入理解一下`kmp`算法。

`kmp`算法匹配的过程中主串的索引在匹配失败时是不会回退的，只有模式串的索引根据`next`数组进行回退。暴力字符串匹配主串和模式串的索引在匹配失败时都会回退。相比之下，`kmp`算法的匹配速度能快很多。

## 1.前缀和后缀

首先要弄清什么是**前缀**和**后缀**，这是理解`kmp`算法的关键。

**前缀**是对于一个长为`len`的字符串，索引区间在`[0,i]`上的子串，其中 `len-1 > i >= 0`。

**后缀**是对于一个长为`len`的字符串，索引区间在`[j,len-1]`上的子串，其中 `len > j > 0`。

比如，字符串`ldtech`，其所有前缀下图：

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp01.png)

其所有后缀如下图：

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp02.png)

## 2.`next`数组的含义

`next`数组又叫前缀表，`kmp`算法的关键就是构造`next`数组，`next`数组到底是什么呢？

举个例子，主串`str`为：`ababadabcee`，模式串`pat`为：`abadabce`。

`next[i]`就是模式串`pat`在`[0,i]`区间上相同前后缀的最大长度。这句话看起来比较抽象，但是一定要多看几遍理解透。我特意画了张图来帮助大家来理解，结合这张图也许你就能豁然开朗。

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp03.png)

这些最长相同前后缀的长度就组成了`next`数组，`next[i]`实际上就是`[0,i]`区间上一个符合条件的前缀长度，这也是`next`数组称为前缀表的原因。

## 3.构造`next`数组

`next`数组的构造过程，实际上就是**模式串的最长后缀作为主串**，**最长前缀作为模式串**进行字符串匹配的一个过程。又是抽象的的一句话，别着急，慢慢往下看。

比如主串`str`为：`ababadabcee`，模式串`pat`为：`abadabce`，那么我们要得到`next`数组只需要将模式串`pat`的最长后缀`badabce`作为主串，最长前缀`abadabc`作为模式串进行匹配。

假设`i`，`j`均为模式串`pat`的索引，`i`遍历最长后缀，故`i`的范围为`[1,7]`，`j`遍历最长前缀，故`j`的范围为`[0,6]`，`pat`的长度为`8`。

匹配过程如下：

1. 初始化`i=1`，`j=0`，`next[0]=0`。
2. 比较`pat[i]`和`pat[j]`，如果`pat[i]==pat[j]`，进入**步骤3**。如果`pat[i]!=pat[j]`，进入**步骤4**。
3. `next[i]=j+1`，`++i`，`++j`，`i`小于模式串`pat`的长度就进入**步骤2**。否则就结束。
4. 如果`j==0`，那么`next[i]=0`，`++i`，`i`小于模式串`pat`的长度就进入**步骤2**。 否则`j=next[j-1]`，进入**步骤2**。

下图展示了完整的匹配流程：

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp04.png)

整个匹配的过程`i`是不回头的，`j`是根据`next`数组进行回退。

`pat[i]==pat[j]`时，`next[i]=j+1`是啥意思呢？`pat[i]==pat[j]`说明`pat`区间`[0,j]`和`pat`区间`[i-j,i]`是匹配的，可以保证他们是`[0,i]`区间的最长相同前后缀。此时前缀串长度为`j+1`，那么前缀表`next[i]=j+1`。如下图可以算出`next[5]=2`。

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp05.png)

`pat[i]!=pat[j]`时，为啥`j=next[j-1]`呢？`pat[i]!=pat[j]`，前面的匹配过程已经存储了`next[j-1]`的信息，只需将`j`进行回退到区间`[0,j-1]`的前缀的下一个位置`next[j-1]`继续匹配即可。如下图：

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp06.png)

代码如下：

```cpp

 void create_next(string pat, vector<int>& next) {
  int len = pat.length();
  next[0] = 0;
  int i = 1, j = 0;
  while(i < len) {
    if (pat[i] == pat[j]) {
      //把前缀串的长度赋给next[i]
      next[i] = j + 1;
      ++i;
      ++j;
    } else {
      //匹配失败且前缀串的长度为0
      if (j == 0) {
        next[i] = 0;
        ++i;
      } else {
        //匹配失败跳到前缀串的下一个元素继续匹配
        j = next[j-1];
      }
    }
  }
}
```

根据上面的步骤，我们就根据模式串`pat`得到了`next`数组。接下来就根据`next`数组来进行**主串**和**模式串**的匹配。

## 4.主串和模式串匹配

还是沿用了上面的例子主串`str`为：`ababadabcee`，模式串`pat`为：`abadabce`。

假设`i`为主串的索引，`j`为模式串的索引，`i`的范围为`[0,10]`，`j`的范围为`[0,7]` 。

匹配过程如下：

1. 初始化`i=1`，`j=0`，`next[0]=0`。
2. 比较 `str[i]`和`pat[j]`，如果`str[i]==pat[j]`，进入**步骤3**。如果**str[i]!=pat[j]**，进入**步骤4**。
3. `++i`，`++j`，`i`小于主串长度且`j`小于模式串长度就进入**步骤2**。否则就匹配成功。
4. 如果`j==0`，那么`next[i]=0`，`++i`，`i`小于主串长度就进入**步骤2**。 否则`j=next[j-1]`，进入**步骤2**。

匹配过程如下图：

![](https://gitee.com/ldtech007/picture/raw/master/kmp/kmp07.png)

整个匹配的过程和`next`数组生成的过程非常相似，`i`是不回头的，`j`是根据`next`数组进行回退。理解了`next`数组的生成过程，就很容易理解主串和模式串的匹配过程。

代码如下：

```cpp

int strStr(string str, string pat) {
  int str_len = str.length();
  int pat_len = pat.length();
  vector<int> next(pat_len, 0);
  create_next(pat, next);
  int i = 0, j = 0;
  while (i < str_len) {
    if (str[i] == pat[j]) {
      ++i;
      ++j;
      if (j == str_len) {
        return i - j;
      }
    } else {
      //匹配失败且前缀串的长度为0
      if (j == 0) {
        ++i;
      } else {
        //匹配失败跳到前缀串的下一个元素继续匹配
        j = next[j-1];
      }
    }

  }
  return -1;
}
```

输入主串`str`和模式串`pat`，`strStr`返回`pat`在`str`中的起始索引值。

## 5.复杂度分析

**时间复杂度：** 生成`next`数组需要遍历一遍模式串，主串和模式串匹配需要遍历一遍主串，故时间复杂度为*O(m+n)*，其中`m`为主串长度，`n`为模式串长度。

**空间复杂度：** 生成`next`数组需要一个长为`n`的数组，主串和模式串匹配只需要两个索引，故空间复杂度为*O(n)*，其中`n`为模式串长度。