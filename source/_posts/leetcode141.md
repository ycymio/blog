---
title: LeetCode 141 Linked List Cycle
date: 2016-03-06 15:32:41
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **Given a linked list, determine if it has a cycle in it.
   Follow up:
      Can you solve it without using extra space?**
</div>
  
</blockquote>


在不使用额外空间的前提下，能否判断一个链表是否有环路?
<!--more-->
#### 思路：
  设置两个指针，一个快指针，一个慢指针。每次快指针总比慢指针多走一步。最后可能出现两种结果：
  * 快指针追上了慢指针 --> 存在环路
  * 或者快指针变为空指针 --> 不存在环路

#### 考虑特殊情况：
  * 链表只有一个结点，或者链表为空。
  
#### 代码：
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
	    public boolean hasCycle(ListNode head) {
          if( head == null || head.next == null){
               return false;
          }
          ListNode fast = head;
          ListNode slow = head;
          while( fast != null && fast.next != null){
                fast = fast.next.next;
                slow = slow.next;
     	        if( fast == slow ){
                    break;
                }
          }
          if( fast == slow ){
               return true;
     	  }
          else{
             return false;
         }
      }
	}
```