---
title: 链表双指针：到底该走几步？
date: 2020-08-01 16:27:21
tags: [leetcode,链表]
categories: [leetcode]
---

环形链表II和相交链表

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



##  相交链表

[相交链表]( https://leetcode-cn.com/problems/intersection-of-two-linked-lists/ )

找到两个单链表相交的起始节点，记链表长度分别为`a`和`b`，可以考虑`fast`结点先走 `abs(a-b)` 步， 然后`fast`和`slow`指针在同时往后走直到相交结点。但是获得`lenA`和`lenB`需要多次遍历链表。

现在考虑如何减少遍历的次数。

设链表`A`的长度为`a`，链表`B`的长度为`b`，`A`到相交结点的距离为`c`,`B`到相交节点的距离为`d`。

显然可以得到两者相交链表的长度：`a - c = b - d`，

 变换一下式子得到：`a + d = b + c`。

用一个指针从链表`A`出发，到末尾后就从`B`出发，用另一个指针从`B`出发，到末尾后从`A`出发，

当前一个指针走了`a+d`步数时，后一个指针走了`b+c`,两步数相等，即走到了相交节点。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode q = headA, p = headB;
        while (q != p) {
            if (q != null) {
                q = q.next;
            } else {
                q = headB;
            }
            if (p != null) {
                p = p.next;
            } else {
                p = headA;
            }
        }
        return q;
    }
}
```

