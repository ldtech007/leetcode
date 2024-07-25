> *题目链接：* https://leetcode.cn/problems/remove-nth-node-from-end-of-list/
>
>*视频题解：* https://www.bilibili.com/video/BV1hs421M7Ke/

# LeetCode 19. 删除链表的倒数第 N 个结点

## 题目描述

给你一个链表，删除链表的倒数第 `n` 个结点，并且返回链表的头结点。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0019-01.png)

```
输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
```

## 思路解析

根据题目描述中的例子，如果要删除倒数第`2`个节点，那么需要拿到指向倒数第`3`个节点的指针`pre`。执行`pre->next = pre->next->next`，即可删除倒数第`2`个节点。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0019-02.png)

寻找倒数第三个节点的算法如下：

* 创建一个虚拟头节点`tempHead`，`tempHead->next = head`，定义两个指针`left = tempHead`，`right = head`。`right`先走`2`步，然后`left`和`right`同时往前走，直到`right`指向空节点，这个时候`left`指向的节点就是倒数第`3`个节点。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0019-03.png)

有些同学会疑惑，怎么知道`right`要让`left`领先几步？这个时候我们可以倒推，根据例子在纸上画个简单的链表，让`right`指向最终的状态，最后一个节点的下一个节点，即`NULL`节点。`left`指向要删除节点的前一个节点，然后数一数有几个箭头，就让`right`领先`left`几步。

 由简单的例子推广到要删除链表的倒数第`n`个节点，在创建虚拟头节点`tempHead`的情况下，`left = tempHead` 就可以先让`right`指针领先`left`指针 `n + 1` 步，如果一开始`right`指针指向`head`节点(即`tempHead->next`)，就需要先走`n`步。

为什么要创建一个虚拟头节点呢？增加了一个虚拟头节点边界情况都会包含在主逻辑中，整体的算法逻辑更清晰。如果不使用虚拟头节点，会增加一些边界情况的处理，有兴趣的同学可以尝试实现一下。增加虚拟头节点也属于处理一些链表题目的小技巧。

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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        //创建虚拟节点
        ListNode* tempHead = new ListNode(0, head);
        ListNode* left = tempHead;
        ListNode* right = head;
        //right先走n步
        while (n > 0 && right) {
            right = right->next;
            n -= 1;
        }
        while (right) {
            left = left->next;
            right = right->next;
        }
        //删除倒数第n个节点
        ListNode* temp = left->next;
        left->next = left->next->next;
        delete temp;
        return tempHead->next;
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
    public ListNode removeNthFromEnd(ListNode head, int n) {
        // 创建虚拟节点
        ListNode tempHead = new ListNode(0, head);
        ListNode left = tempHead;
        ListNode right = head;
        // right先走n步
        while (n > 0 && right != null) {
            right = right.next;
            n -= 1;
        }
        while (right != null) {
            left = left.next;
            right = right.next;
        }
        // 删除倒数第n个节点
        ListNode temp = left.next;
        left.next = left.next.next;
        temp = null;
        return tempHead.next;
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
    def removeNthFromEnd(self, head: Optional[ListNode], n: int) -> Optional[ListNode]:
        # 创建虚拟节点
        tempHead = ListNode(0, head)
        left = tempHead
        right = head
        # right先走n步
        while n > 0 and right:
            right = right.next
            n -= 1
        while right:
            left = left.next
            right = right.next
        # 删除倒数第n个节点
        temp = left.next
        left.next = left.next.next
        del temp
        return tempHead.next
```

## 复杂度分析

**时间复杂度：** 只需要遍历一遍链表，所以时间复杂度为*O(n)*，其中`n`为链表的长度。

**空间复杂度：** 需要借助一个虚拟头节点，所以空间复杂度为*O(1)*。
