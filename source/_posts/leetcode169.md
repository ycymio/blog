---
title: LeetCode 169 Majority Element
date: 2016-03-15 10:38:08
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.

You may assume that the array is non-empty and the majority element always exist in the array.
**
</div>

</blockquote>

给定大小为n的数组，找到占大多数(出现了超过⌊n/2⌋次)的元素。
假设给定的数组不为空，并且占大多数的元素一定存在。

<!--more-->
#### 思路: 
以前一直用的一个方法:
首先记录第一个元素及其出现次数n=1，下一个如果再遇到该元素则n++，如果没有遇见则n--，一旦n小于0，就用当前元素来代替原先记录的元素。遍历到最后，最后记录的那个元素即是我们所需的元素。
然而...Java里的速度没有Array.sort()排列完去最中间的数(因为肯定是一半)快...

#### 代码：
```java
    // 以前的方法
    public class Solution {
        public int majorityElement(int[] nums) {
            int x = nums[0];
            int num = 1;
            for( int i = 1; i < nums.length; i++){
                if( x == nums[i]){
                    num++;
                }
                else{
                    num--;
                }
                if( num < 0 ){
                    x = nums[i];
                    num = 1;
                }
            }
            return x;
        } 
    }
    
    // Java sort()
    public class Solution {
        public int majorityElement(int[] nums) {
            int len = nums.length;
            Arrays.sort(nums);
            return nums[len/2];
        } 
    }
```
    