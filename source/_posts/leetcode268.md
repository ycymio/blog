---
title: LeetCode 268 Missing Number
tags:
  - LeetCode
  - Bit Manipulation
categories:
  - OJ
  - LeetCode
  - Java
date: 2016-04-22 20:20:31

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
**
Given an array containing n distinct numbers taken from 0, 1, 2, ..., n, find the one that is missing from the array.
**
For example,
Given nums = [0, 1, 3] return 2.
Note:
Your algorithm should run in linear runtime complexity. Could you implement it using only constant extra space complexity?
</div>
</blockquote>

在n个不同数字的数组中找到0到n之间缺少的值。
<!--more-->
#### 思路:
1. 数组元素不一定是有序的，所以最简单的方法就是先排序，再二分查找缺少的值。
如果是近乎有序的数组，这种方法比较快，但对于一般情况，耗时要久些。

2. 把所有数组元素相加，与原数组元素之和相比较，其差值就是缺少的值。
这种方法容易元素之和溢出。

3. 用“异或XOR”。

关于异或: 
+ 如果a、b两个值不相同，则异或结果为1。如果a、b两个值相同，异或结果为0。
+ 一位数与1异或，取其反；与0异或，为其本身。(一串1，或者混搭，都会有很不错的效果)
+ 一个数与其本身异或，结果为0。
(一个数与其本身异或，结果为0) + (一个数与0异或，结果为其本身) + (异或满足交换律和结合律) -> a^b^b=a

再扩展下，变成: a^b^b^c^c^d^d^....=a。a后的异或对象顺序改变对其结果没有影响。**也就是说，一个数与一些数异或两次，其结果为原先的数本身**。
放到这里，就是将数组中所有的元素异或，并与0到n的所有值异或，所得的结果就是缺少的值。

扩展: <a href="http://www.cnblogs.com/danh/archive/2010/12/24/1915810.html">异或 ^ 的几个作用</a>

#### 代码:
```java
// Binary Search
public class Solution {
    public int missingNumber(int[] nums) {
        Arrays.sort(nums);
        int first = 0;
        int last = nums.length - 1;
        while( first <= last ){
            int mid = (first + last)/2;
            if( nums[mid] != mid ){
                last = mid - 1;
            }
            else{
                first = mid + 1;
            }
        }
        return first;
    }
}
```

```java
// XOR
public class Solution {
    public int missingNumber(int[] nums) {
        int result = 0;
        for(int i = 0; i < nums.length; ++i){
            result ^= i;
            result ^= nums[i];
        }
        result ^= nums.length;
        return result;
    }
}
```