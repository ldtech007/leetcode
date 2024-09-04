> *题目链接：* https://leetcode.cn/problems/rotate-image/
>
> *视频题解：* https://www.bilibili.com/video/BV1FaHbecE9V/

# LeetCode 48. 旋转图像

## 题目描述

给定一个 *n × n* 的二维矩阵 `matrix` 表示一个图像。请你将图像顺时针旋转 `90` 度。

你必须在 **原地** 旋转图像，这意味着你需要直接修改输入的二维矩阵。**请不要** 使用另一个矩阵来旋转图像。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0048-01.png)

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[[7,4,1],[8,5,2],[9,6,3]]
```

## 思路解析

这种题目没有什么好办法，只能由外到内一层一层的旋转。题目要求原地旋转，其实就是要求空间复杂度保证在 *O(1)*。

旋转步骤如下：

1. 定义矩阵的边界`left = top = 0`，`right = bottom = matrix.size() - 1`。
2. 先借助一个整型变量，旋转四个角的元素。
3. 再借助同一个整型变量旋转基于四个角偏移的元素，最多偏移`right - left - 1`。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0048-02.png)

4. 把最外层全部旋转以后，缩小矩阵的边界，`++left`、`++top`、`--right`、`--bottom`。如果`left < right`，进入`步骤2`，否则结束。

## C++代码

```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int left = 0, right = matrix.size() - 1;
        while (left < right) {
            for (int i = 0; i < right - left; ++i) {
                //因为矩阵是正方形的
                int top = left, bottom = right;
                //保存做左上角的元素
                int topleft = matrix[top][left + i];
                //左下角旋转到左上角
                matrix[top][left + i] = matrix[bottom - i][left];
                //右下角旋转到左下角
                matrix[bottom - i][left] = matrix[bottom][right - i];
                //右上角旋转到右下角
                matrix[bottom][right - i] = matrix[top + i][right];
                //左上角旋转到右上角
                matrix[top + i][right] = topleft;
            }
            ++left;
            --right;
        }
    }
};
```

## java代码

```java
class Solution {
    public void rotate(int[][] matrix) {
        int left = 0, right = matrix.length - 1;
        while (left < right) {
            for (int i = 0; i < right - left; ++i) {
                // 因为矩阵是正方形的
                int top = left, bottom = right;
                // 保存左上角的元素
                int topleft = matrix[top][left + i];
                // 左下角旋转到左上角
                matrix[top][left + i] = matrix[bottom - i][left];
                // 右下角旋转到左下角
                matrix[bottom - i][left] = matrix[bottom][right - i];
                // 右上角旋转到右下角
                matrix[bottom][right - i] = matrix[top + i][right];
                // 左上角旋转到右上角
                matrix[top + i][right] = topleft;
            }
            ++left;
            --right;
        }
    }
}
```

## python代码

```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        left, right = 0, len(matrix) - 1
        while left < right:
            for i in range(right - left):
                # 因为矩阵是正方形的
                top, bottom = left, right
                # 保存左上角的元素
                topleft = matrix[top][left + i]
                # 左下角旋转到左上角
                matrix[top][left + i] = matrix[bottom - i][left]
                # 右下角旋转到左下角
                matrix[bottom - i][left] = matrix[bottom][right - i]
                # 右上角旋转到右下角
                matrix[bottom][right - i] = matrix[top + i][right]
                # 左上角旋转到右上角
                matrix[top + i][right] = topleft
            left += 1
            right -= 1
```

## 复杂度分析

**时间复杂度：** 需要遍历一遍矩阵`matrix`，所以时间复杂度是*O(mn)*，其中`m`是矩阵的行数，`n`是矩阵的列数。

**空间复杂度：** 只需要借助几个整型变量，故空间复杂度是*O(1)*。
