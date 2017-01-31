---
title: LeetCode 237 Delete Node in a Linked List
date: 2016-03-09 09:06:06
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.
   Supposed the linked list is 1 -> 2 -> 3 -> 4 and you are given the third node with value 3, the linked list should become 1 -> 2 -> 4 after calling your function.

**
</div>

</blockquote>

给定链表的某个结点，要求删除该结点(该结点不是尾结点)。
<!--more-->

#### 思路: 
    之前有想到，我们不知道该结点的前面的那个结点，就无法修改它的链接，那该如何处理？
    跳出这个圈，我们不是一定要修改该链接，只是删除一个结点，而不是释放给定结点的存储空间，所以可以删除所给结点的后一个结点，而这后一个结点的值完全复制给所给的结点。
    
#### 代码：
```java
    /**
     * Definition for singly-linked list.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) { val = x; }
     * }
     */
     public class Solution {
         public void deleteNode(ListNode node) {
             if( node == null || node.next == null)
                 return;
             // copy the node.
             node.val = node.next.val;
             node.next = node.next.next;
         }
     }
```