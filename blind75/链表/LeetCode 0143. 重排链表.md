> *题目链接：* https://leetcode.cn/problems/reorder-list/

# LeetCode 143. 重排链表

## 题目描述

给定一个单链表 L 的头节点 head ，单链表 L 表示为：

L<sub>0</sub> → L<sub>1</sub> → … → L<sub>n-1</sub> → L<sub>n</sub>

请将其重新排列后变为：

L<sub>0</sub> → L<sub>n</sub> → L<sub>1</sub> → L<sub>n - 1</sub> → L<sub>2</sub> → L<sub>n - 2</sub> → …

不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。

**举个例子：**

![](../../pic/lc-0143-01.png)

```
输入：head = [1,2,3,4]
输出：[1,4,2,3]
```

## 思路解析

本题如果是重排**数组**，可以定义两个指针`left`，`right`。一个指向数组的头，一个指向数组的尾，访问完两个元素后同时移动两个指针`++left`，`--right`，直到`left >= right`。但是**单向链表是没办法逆序遍历的**，所以我们需要把链表基于中间节点截成两部分`l1`和`l2`，对第二部分`l2`进行反转，然后交替合并`l1`和`l2`。

反转链表在「LeetCode 206.反转链表」中已经详细讲解过，本题的关键其实就是怎样把链表一分为二。

1. 如果链表节点数为奇数

![](../../pic/lc-0143-02.png)

2. 如果链表节点数为偶数

![](../../pic/lc-0143-03.png)

上面的两种情况均可通过下面的算法来实现：

1. 定义两个指针`slow = head`，`fast = head->next`。
2. 当`fast`且`fast->next`不为空，`slow`走一步，`fast`走两步。
3. 当`fast`或`fast->next`为空，`slow`指向的节点就是第一部分的尾节点，`slow->next`指向的节点就是第二部分的头节点。

在纸上模拟一遍上面的算法，可以帮助大家快速理解。

## C++代码

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    void reorderList(ListNode* head) {

        ListNode* slow = head;
        ListNode* fast = head->next;
        while (fast && fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        //反转后半段链表
        ListNode* second = reverseList(slow->next);
        slow->next = nullptr;
        ListNode* first = head;
        //交替合并first和second
        while (second) {
            ListNode* tmpNode1 = first->next;
            ListNode* tmpNode2 = second->next;
            first->next = second;
            second->next = tmpNode1;
            first = tmpNode1;
            second = tmpNode2;
        }
    }

     ListNode* reverseList(ListNode* head) {

        if (!head)
            return head;
        
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur) {
            ListNode* tmpNext = cur->next;
            cur->next = pre;
            pre = cur;
            cur = tmpNext;
        }
        return pre;
    }
};
```

## 复杂度分析

**时间复杂度：** 需要遍历`3`次链表，所以时间复杂度为*O(n)*，其中`n`为链表的长度。

**空间复杂度：** 只使用了几个指针，所以空间复杂度为*O(1)*。
