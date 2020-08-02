---
title: 链表双指针：到底该走几步？
date: 2020-08-01 16:27:21
tags: [leetcode,链表]
categories:[leetcode]
---

环形链表II和链表中的相交结点 

<!--more-->

##  环形链表

[环形链表I]( https://leetcode-cn.com/problems/linked-list-cycle/ )

[环形链表II]()

判断一个链表是否有环，最基本的双指针方法。

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        //快慢指针 slow每次走1格 fast每次走2格
        if(head==null) return false;
        ListNode slow = head;
        if(slow.next==null) return false;
        ListNode fast = slow.next.next;
       
        while (fast != null) {
            if (slow == fast) return true;  //当快慢指针相遇 说明存在环
            else {
                slow = slow.next;
                if(fast.next==null) return false;
                fast = fast.next.next;
            }
        }
        //没有环的最终会推出循环
        return false;
    }
}
```

但是，如果想要判断环形链表中环的起点时，就要考虑快慢指针走了多少次。

假设从链表起点出发到环起点的距离为`a`，环的长度为`b`，现有快慢指针`fast`和`slow`第一次相遇。

- `fast` 走的步数是`slow`步数的 2倍，即`f = 2s`；

- `fast `比 `slow`多走了 n 个环的长度，即`f = s + nb`；（ **解析**: 双指针都走过`a`步，然后在环内绕圈直到重合，重合时 `fast` 比 `slow` 多走环的长度整数倍 ）

- 以上两式相减得：`f = 2nb`, `s = nb`

现在考虑环的起点位置为：`a+nb`，所以， `slow`指针 **位置不变** ，将`fast`指针重新 **指向链表头部节点** ；`slow`和`fast`同时每轮向前走 `a` 步，两个结点再次重合，该节点即为环的起点。 

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null || head.next == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        while (true) {
            slow = slow.next;
            if (fast==null||fast.next == null) {
                return null;
            } else {
                fast = fast.next.next;
            }
            if (slow == fast) break;
        }
        fast = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}
```





