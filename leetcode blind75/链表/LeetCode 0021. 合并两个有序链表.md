> *题目链接：* https://leetcode.cn/problems/merge-two-sorted-lists/
>
>*视频题解：* https://www.bilibili.com/video/BV17w4m1Y7CJ/

# LeetCode 21. 合并两个有序链表

## 题目描述

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0021-01.png)

```
输入：l1 = [1,2,4], l2 = [1,3,4]
输出：[1,1,2,3,4,4]
```

## 思路解析

本题的思路比较简单。创建一个虚拟节点`newHead`，定义一个*始终指向新链表尾部*的指针`tail`。

1. 当链表指针`l1`和`l2`都不为空，如果`l1->val <= l2->val`，就把`l1`指向的节点挂到`tail`后面，同时把`l1`往后移动一步，`tail`也向后移动一步。反之对于`l2->val <= l1->val`亦然。
2. 当链表指针`l1`或`l2`为空，把不为空的`l1`或`l2`挂到`tail`后面，返回`newHead->next`。

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
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        //创建一个虚拟的头节点方便后面操作
        ListNode* newHead = new ListNode();
        ListNode* tail = newHead;
        while (list1 && list2) {
            //选取val较小的节点放到tail后面
            if (list1->val <= list2->val) {
                tail->next = list1;
                list1 = list1->next;
            } else {
                tail->next = list2;
                list2 = list2->next;
            }
            //保证tail一直指向新链表的最后一个节点
            tail = tail->next;
        }
        //把剩下的有序节点挂到tail后面
        if (list1) {
            tail->next = list1;
        } else if (list2) {
            tail->next = list2;
        }
        return newHead->next;
    }
};
```

## java代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        // 创建一个虚拟的头节点方便后面操作
        ListNode newHead = new ListNode();
        ListNode tail = newHead;
        while (list1 != null && list2 != null) {
            // 选取val较小的节点放到tail后面
            if (list1.val <= list2.val) {
                tail.next = list1;
                list1 = list1.next;
            } else {
                tail.next = list2;
                list2 = list2.next;
            }
            // 保证tail一直指向新链表的最后一个节点
            tail = tail.next;
        }
        // 把剩下的有序节点挂到tail后面
        if (list1 != null) {
            tail.next = list1;
        } else if (list2 != null) {
            tail.next = list2;
        }
        return newHead.next;
    }
}
```

## python代码

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        # 创建一个虚拟的头节点方便后面操作
        newHead = ListNode()
        tail = newHead
        while list1 and list2:
            # 选取val较小的节点放到tail后面
            if list1.val <= list2.val:
                tail.next = list1
                list1 = list1.next
            else:
                tail.next = list2
                list2 = list2.next
            # 保证tail一直指向新链表的最后一个节点
            tail = tail.next
        # 把剩下的有序节点挂到tail后面
        if list1:
            tail.next = list1
        elif list2:
            tail.next = list2
        return newHead.next
```

## 复杂度分析

**时间复杂度：** *O(m+n)*，其中`m`是链表`l1`的长度，`n`是链表`l2`的长度。

**空间复杂度：** *O(1)*。
