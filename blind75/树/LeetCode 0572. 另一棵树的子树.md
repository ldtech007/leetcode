> *题目链接：* https://leetcode.cn/problems/subtree-of-another-tree/

# LeetCode 572. 另一棵树的子树

## 题目描述

给你两棵二叉树 `root` 和 `subRoot` 。检验 `root` 中是否包含和 `subRoot` 具有相同结构和节点值的子树。如果存在，返回 `true` ；否则，返回 `false` 。

二叉树 `tree` 的一棵子树包括 `tree` 的某个节点和这个节点的所有后代节点。`tree` 也可以看做它自身的一棵子树。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0572-01.png)

```
输入：root = [3,4,5,1,2], subRoot = [4,1,2]
输出：true
```

## 思路解析

首先明确**子树**的概念，对于下图，`subRoot`中节点虽然和`root`中节点的值相等，但是`subRoot`中`节点2`的左孩子是`NULL`，`root`中`节点2`的左孩子是`节点0`，这违反了结构相同，所以`subRoot`不是`root`的子树。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0572-02.png)

明确了子树的概念，此问题的递归步骤如下：
1. `root`和`subRoot`是否相等(`root`本身也是自己的子树)。
2. `subRoot`是否是`root->left`的子树。
3. `subRoot`是否是`root->right`的子树。

上述三个点只要有一点成立，就说明`subRoot`是`root`的子树。

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
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        //递归结束的条件，空节点NULL是任意树的子树
        if (!subRoot) return true;
        //递归结束的条件，此时subRoot不为NULL
        if (!root) return false;
        //递归结束的条件
        if (isSameTree(root, subRoot)) return true;
        //子问题
        return isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot);
    }

    bool isSameTree(TreeNode* p, TreeNode* q) {
        //递归结束的条件
        if (!p && !q) {
            return true;
        }
        //递归结束的条件
        if (!p || !q || p->val != q->val) {
            return false;
        }
        //子问题
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }
};
```

## 复杂度分析

**时间复杂度：** 由于`s`的每一个节点都要与`t`进行去匹配，所以时间复杂度为 *O(ST)*，其中`S`为`s`的节点数量，`T`为`t`的节点数量。

**空间复杂度：** 匹配的过程用到了递归，递归会用到函数栈，栈的最大长度基本和`t`的高度一致，故空间复杂度和`t`的高度相关。

