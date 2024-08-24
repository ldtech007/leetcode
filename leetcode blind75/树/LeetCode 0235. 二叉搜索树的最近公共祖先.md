> *题目链接：* https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-search-tree/
>
> *视频题解：* https://www.bilibili.com/video/BV14ieKeGERA/

# LeetCode 235. 二叉搜索树的最近公共祖先

## 题目描述

给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 `T` 的两个结点 `p`、`q`，最近公共祖先表示为一个结点 `x`，满足 `x` 是 `p`、`q` 的祖先且 `x` 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

例如，给定如下二叉搜索树: `root = [6,2,8,0,4,7,9,null,null,3,5]`

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0235-01.png)

**举个例子：**

```
输入: root = [6,2,8,0,4,7,9,null,null,3,5], p = 2, q = 8
输出: 6 
解释: 节点 2 和节点 8 的最近公共祖先是 6。
```

## 思路解析

本题比较简单，主要是利用**二叉搜索树根节点的值大于左子树所有节点的最大值，小于右子树所有节点的最小值这个特点**，算法如下：

1. 从二叉搜索树的根节点开始比较，如果当前节点的值比`p`和`q`的值都大，就到当前节点的左子树去中寻找公共祖先。
2. 从二叉搜索树的根节点开始比较，如果当前节点的值比`p`和`q`的值都小，就到当前节点的右子树中去寻找公共祖先。
3. 以上都不成立，则当前节点为二叉搜索树的最近公共祖先。

## C++代码

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {

        TreeNode* cur_node = root;
        while (cur_node) {
             //p,q在cur_node左边
            if (p->val < cur_node->val && q->val < cur_node->val) {
                cur_node = cur_node->left;
            //p,q在cur_node右边
            } else if (p->val > cur_node->val && q->val > cur_node->val) {
                cur_node = cur_node->right;
            } else {
                return  cur_node;
            }
        }
        return NULL;
    }
};
```

## 复杂度分析

**时间复杂度：** 时间复杂度跟二叉树的高度相关。

**空间复杂度：** 只需要用到一个临时节点，所以空间复杂度为*O(1)*。

