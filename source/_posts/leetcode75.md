---
title: LeetCode 75 Sort Colors
tags:
  - LeetCode
categories:
  - OJ
  - LeetCode
  - Java
date: 2016-03-20 13:11:35

---


<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **
Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.
Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.
 **
Note:
You are not suppose to use the library's sort function for this problem.
Follow up:
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
Could you come up with an one-pass algorithm using only constant space?
</div>
  
</blockquote>

给定由0，1，2三个元素组成的数组，按 0,0,...,0,1,..,1,2,...,2 排序。
要求: 循环一次，用常量的空间。

<!--more-->

#### 思路:
有关Two Pointers的题。
不考虑时间复杂度和空间复杂度的比较简单的解法: 
1. 排序 Arrays.sort()，直接可以搞定了。
2. 记录数量，0，1，2分别的数量是多少，再一个一个赋值就好。

但题目加了限制，只能循环一次，就要考虑 two pointers 的方法了。
two pointers的题会很自然的想到可以做两个标记，一个标记数组头(假设为low)，一个标记数组尾(假设为high)，然后有一个0，low++，有一个2，high-- 这样做。后来就有点迷茫，做四个指针，一个指向从头算的最后一个0，一个指向从尾算的最后一个2，还有一个在中间如何游走呢。遍历的同时通过记录1的数量，再赋值么。这样的话，就不止是循环一次了。因而思维卡在了记录数量上。
>可以通过数字交换一次遍历解决问题:
做两个标记，一个标记数组头(假设为low)，一个标记数组尾(假设为high)，如何处理数字1是关键，这里重点是只从数字头遍历，只处理在遍历的指针的数据，而不像求2Sum时。并且，在low指向的元素之前的元素都是0，在high指向的元素之后的都是2.
有一个专门遍历的指针i，指向数组第一个元素。
如果它指向的值为2，则与high指针指向的元素交换，并且high--。(此时不考虑high后来指向的数值是多少，思路更简洁明确)。这里由于i此时指向的值之前未做判断，指针i不移动。(由于high值减小了，最终停止的条件仍为 i <= high，所以i不移动并不会增加复杂度)
如果它指向的值为0，则与low指针指向的元素交换，并且low++。这里，我们还能确定low指向的元素到i指向的元素之间(包括这两者)不是0，就是1。因此，两者交换后，i指向的值肯定不为2，为0或是1并不重要。所以i也要++。
如果指向的是1，就直接i++。

所以三个指针的作用： 
* low: 在low指向的元素之前的元素都是0
* high: 在high指向的元素之后的都是2
* i: **遍历的指针**，并且，**在i指向的元素之前和low之后的指针都是1**。

i指针的作用很关键。

Note: 用“记录数量，再一个一个赋值”的方法，时间比较快。(交换数据的时间也许比较长)

另一个方法：
```java
    // one pass in place solution
    void sortColors(int A[], int n) {
        int n0 = -1, n1 = -1, n2 = -1;
        for (int i = 0; i < n; ++i) {
            if (A[i] == 0) {
                A[++n2] = 2; A[++n1] = 1; A[++n0] = 0;
            }
            else if (A[i] == 1){
                A[++n2] = 2; A[++n1] = 1;
            }
            else if (A[i] == 2) {
                A[++n2] = 2;
            }
        }
    }    
```

n0, n1, n2分别记录之前的元素已大于等于0，大于等于1，大于等于2。方法很巧妙。

#### 代码:
```java
    public class Solution {
        public void sortColors(int[] nums) {
            int low = 0;
            int high = nums.length-1;
            for( int i = 0; i <= high; ){
                if( nums[i] == 2 ){
                    swap(nums, i, high);
                    high--;
                }
                else{
                    if( nums[i] == 0){
                        swap(nums, i, low);
                        low++;
                    }
                    i++;
                } 
            }
        }
    
        private void swap(int[] nums, int a, int b){
            int tmp = nums[a];
            nums[a] = nums[b];
            nums[b] = tmp;
        }
    }
```