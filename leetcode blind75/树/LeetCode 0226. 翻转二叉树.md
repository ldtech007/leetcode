> *题目链接：* https://leetcode.cn/problems/invert-binary-tree/
>
> *视频题解：* https://www.bilibili.com/video/BV1N7YheBE8H/

# LeetCode 226. 翻转二叉树

## 题目描述

给你一棵二叉树的根节点 root ，翻转这棵二叉树，并返回其根节点。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0226-01.png)

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```

## 思路解析

递归可以分为两步：**递归结束条件的处理**和**独立子问题的划分**。

在这里**翻转二叉树**可以划分为**翻转二叉树的左子树**和**翻转二叉树的右子树**两个独立子问题。

只要在翻转二叉树的左右子树以后，对二叉树的左右子树进行交换就可以完成整棵树的翻转。

递归是函数一层一层调用的过程，函数调用实际上是一个入栈出栈的过程，这里的栈就是函数调用栈，你可以用`gdb`调试一个程序通过`bt`命令来查看函数调用栈。这里的递归函数是`invertTree`，它的入参是二叉树的根节点，返回值为是翻转后的二叉树。

整个递归的过程如下图：

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0226-02.png)

## C++代码

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {

        if (!root) {
            return root;
        }
        root->left = invertTree(root->left);
        root->right = invertTree(root->right);

        TreeNode* tmp = root->left;
        root->left = root->right;
        root->right = tmp;
        return root;
    }
};
```
## java代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) {  // 如果节点为空，返回该节点
            return root;
        }

        // 递归地翻转左子树和右子树
        root.left = invertTree(root.left);
        root.right = invertTree(root.right);

        // 交换左子树和右子树
        TreeNode tmp = root.left;
        root.left = root.right;
        root.right = tmp;

        return root;
    }
}
```

## python代码

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:  # 如果节点为空，返回该节点
            return root

        # 递归地翻转左子树和右子树
        root.left = self.invertTree(root.left)
        root.right = self.invertTree(root.right)

        # 交换左子树和右子树
        tmp = root.left
        root.left = root.right
        root.right = tmp
        
        return root
```

## 复杂度分析

**时间复杂度：**  因为需要遍历整棵树所有的节点，所以时间复杂度跟树的总节点数相关。

**空间复杂度：** 因为使用了递归，需要用到一个栈来保存函数的调用，栈的最大长度为树的高度，所以空间复杂度跟树的高度有关。

