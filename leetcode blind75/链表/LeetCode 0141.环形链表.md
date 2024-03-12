> *题目链接：* https://leetcode.cn/problems/linked-list-cycle/

# LeetCode 141.环形链表

## 题目描述

给你一个链表的头节点 `head` ，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，评测系统内部使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 `0` 开始）。**注意：`pos` 不作为参数进行传递** 。仅仅是为了标识链表的实际情况。

*如果链表中存在环* ，则返回 `true` 。 否则，返回 `false` 。

**举个例子：**

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0141-01.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```
## 思路解析

### 方法一 使用hashset

使用一个`hashset`很容易判断链表是否有环，只需要一边遍历链表一边用`hashset`记录遍历过的节点，只要重复遍历到某个节点就说明链表存在环。

### C++代码

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {

        if (!head) 
            return false;
        unordered_set<ListNode*> visited;
        ListNode* cur = head;
        while (cur) {
            //节点被访问过，说明有环
            if (visited.find(cur) != visited.end()) {
                return true;
            }
            //把节点存到visited
            visited.emplace(cur);
            cur = cur->next;
        }
        return false;
    }
};
```


### 方法二 快慢指针

定义两个指针`slow`和`fast`，一开始两个指针均指向链表头节点`head`，`slow`每次移动一步，`fast`每次移动两步。

1. 如果链表不存在环，那么`fast`指针会率先移动到`NULL`节点。
2. 如果链表中存在环，`slow`指针和`fast`指针最终都会进入到环里，这个时候两个指针的移动可以看成`fast`指针追`slow`指针。如下图，假设`fast`指针和`slow`指针在环中的距离为`3`，`fast`指针每次移动两步，`slow`指针每次移动一步，每次移动他们之间的距离就会缩小`2 - 1 = 1`，这样他们只需要移动`3`次就可以相遇。同理，如果`fast`指针和`slow`指针在环中的距离为`n`，只需要移动`n`次就可以相遇。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0141-02.png)

### C++代码

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast && fast->next) {
           slow = slow->next;
           fast = fast->next->next;
           //快指针和慢指针相遇说明有环
           if (slow == fast) {
               return true;
           }
        }
        return false; 
    }
};
```

## 复杂度分析

**时间复杂度：** 两种方法的时间复杂度都是*O(n)*，其中`n`是链表的长度。

**空间复杂度：** 使用`hashset`的空间复杂度是*O(n)*，快慢指针的空间复杂度是*O(1)*。
