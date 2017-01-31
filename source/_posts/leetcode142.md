---
title: LeetCode 142 Linked List Cycle II
date: 2016-03-07 11:04:22
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **Given a linked list, return the node where the cycle begins. If there is no cycle, return null.
   Note: Do not modify the linked list.
   Follow up:
      Can you solve it without using extra space?**
</div>
</blockquote>

在不修改链表和不使用额外空间的前提下，返回链表中环路的起始节点，如果不存在，则返回 null。
<!--more-->
#### 思路: 
    在LeetCode 141题的基础上，需要获得链表中环路的起始节点。
    (本来思考第一次while循环，即LeetCode 141的while循环，可以获得环的长度，然后一个点一个点的遍历，然而Time Limit Exceeded...况且这也没技巧，应该找到内在规律再继续的...)
    □ - □ - □ - … - 1 (环路开始) - … - (快慢指针相遇) - … - (连接1) - … 
    这里设环路开始结点距头结点的距离为C, 慢指针追上快指针距环路开始的距离为S, 环路的长度为L。那么:
1. 慢指针所走的距离为 C+S，快指针所走的距离为 C+S+L，也可以为 2\*(C+S)。-> L = C+S
2. 我们要走到环路开始的结点，可以开始走的结点->头结点&快慢指针所在的结点。对于头结点，需要走C次; 对于快/慢结点，需要走(L-S=)C次，所以头结点与慢节点一起遍历，当它们相遇时，即是环路开始的结点。
    
#### 代码:
```java
    /**
     * Definition for singly-linked list.
     * class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) {
     *         val = x;
     *         next = null;
     *     }
     * }
     */
    public class Solution {
        public ListNode detectCycle(ListNode head) {
            if( head == null || head.next == null){
                return null;
            }
            ListNode fast = head;
            ListNode slow = head;
            int length = 0;
            while( fast != null && fast.next != null){
                fast = fast.next.next;
                slow = slow.next;
                length++;
                if(fast == slow){
                    break;
                }
            }
            if( fast != slow ){
                return null;
            }
            while( head != slow ){
                head = head.next;
                slow = slow.next;
            }
            return head;
        }
    }
```