---
title: LeetCode 55 Jump Game
tags:
  - LeetCode
categories:
  - OJ
  - LeetCode
  - Java
date: 2017-01-07 16:47:44

---


<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">** Given an array of non-negative integers, you are initially positioned at the first index of the array.
Each element in the array represents your maximum jump length at that position.
Determine if you are able to reach the last index.
For example:
A = [2,3,1,1,4], return true.
A = [3,2,1,0,4], return false.**</div> </blockquote>

给定一个非负整型数组，你从数组的第一个位置(index为0)出发，每个元素代表你从该位置跳跃的最大长度。判断你是否可以到达数组的最后一个位置。

#### 思路:
难得有一层一层的思路，这里记录下来。

##### 递归初级版
用递归，一层一层迭代，代码如下:
```java
public class Solution {
    public boolean canJump(int[] nums, int startPosition){
        if( startPosition >= nums.length - 1){
            return true;
        }
        int lastPosition = nums[startPosition] + startPosition;
        if( lastPosition >= nums.length - 1){
            return true;
        }
        for( int i = nums[startPosition]; i > 0; --i){
            if( canJump(nums, i+startPosition) == true){
                return true;
            }
        }
        return false;
    }
    public boolean canJump(int[] nums) {
        if( nums == null || nums.length == 0){
            return false;
        }
        if( nums[0] >= nums.length - 1){
            return true;
        }
        for( int startPosition = nums[0]; startPosition > 0; --startPosition){
            if( canJump(nums, startPosition) == true ){
                return true;
            }
        }
        return false;
    }
}
```

结果 69 / 72 test cases passed. Status: Time Limit Exceeded. 意料之中，因为每次递归太过耗时，时间复杂度 O(n<sup>2</sup>)，空间复杂度O(1)。

##### 对递归方法的优化
考虑结果是否可以重复使用。将每次递归的结果存入数组，如果该数组位置已经递归计算过了，就不再计算，直接使用结果。改进代码如下:
```java
public class Solution {
    private int[] jumpResult;
    public boolean canJump(int[] nums, int startPosition){
        if( startPosition >= nums.length - 1 || jumpResult[startPosition] == 1){
            return true;
        }
        if( jumpResult[startPosition] != 0){ 
            return false;
        }
        // 以上根据既定结果返回. 运行到这里说明，之前没有计算过该位置. 
        int lastPosition = nums[startPosition] + startPosition;
        if( lastPosition >= nums.length - 1){
            jumpResult[startPosition] = 1;
            return true;
        }
        for( int i = nums[startPosition]; i > 0; --i){
            if( canJump(nums, i+startPosition) == true){
                jumpResult[startPosition] = 1;
                return true;
            }
        }
        jumpResult[startPosition] = -1;
        return false;
    }
    public boolean canJump(int[] nums) {
        jumpResult = new int[nums.length];
        if( nums == null || nums.length == 0){
            return false;
        }
        if( nums[0] >= nums.length - 1){
            return true;
        }
        for( int startPosition = nums[0]; startPosition > 0; --startPosition){
            if( jumpResult[startPosition] == 1 || canJump(nums, startPosition) == true){
                return true;
            }
        }
        return false;
    }
}
```

结果 Runtime Error Message: Line 17: java.lang.StackOverflowError
Last executed input:
[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1...
由于递归的运用，导致当前线程的栈满。因此需要避开递归的方法。

##### 双重循环
考虑用双重循环，从尾到头计算是否该位置可以完成Jump Game，因为前面的位置是否可以Jump到最后一个位置本来就是依赖后面的位置是否可以Jump的结果。代码如下:
```java
public class Solution {
    private int[] jumpResult;
    public boolean canJump(int[] nums) {
        jumpResult = new int[nums.length];
        if( nums == null || nums.length == 0){
            return false;
        }
        if( nums[0] >= nums.length - 1){
            return true;
        }
        for( int i = nums.length - 1; i >= 0; --i){
            if( nums[i] + i + 1 >= nums.length){
                jumpResult[i] = 1;
                continue;
            }
            for( int j = nums[i]; j >= 0; --j){
                if( jumpResult[j + i] == 1){
                    jumpResult[i] = 1;
                    break;
                }
            }
            if( jumpResult[i] != 1){
                jumpResult[i] = -1;
            }
        }
        return jumpResult[0] == 1? true : false;
    }
}
```
结果 72 / 72 test cases passed. Status: Time Limit Exceeded，超时，最后一个超时的结果是false，样例很特别(可以专门去看看?)，但也说明该方法即使简化了一些，但最坏时间复杂度仍然是O(n<sup>2</sup>)，这并没有达到想象的优。

##### 正确姿势的贪心算法(?)
那么，如果用一个数来记录最近的可达最后一位的那个位置呢，因为，其实只需要离的最近的那个可达位置，离的远的反而不起效果，因为又不是说跳的次数最少~因而就产生了如下代码: 
```java
public class Solution {
    public boolean canJump(int[] nums) {
        if( nums == null || nums.length == 0){
            return false;
        }
        if( nums[0] >= nums.length - 1){
            return true;
        }
        int rightPlace = nums.length;
        for( int i = nums.length - 1; i >= 0; --i){
            if( nums[i] + i + 1 >= nums.length ||  nums[i] + i >= rightPlace){
                rightPlace = i;
            }
        }
        return rightPlace == 0? true : false;
    }
}
```
时间复杂度 O(n)，空间复杂度O(1)。这才是想要的贪心算法吧。总是兜兜转转，从空间复杂度为O(n)减到O(1)，估计刷题刷的太少了。and 这方法是8s，别人7s，6s又是怎么达到的啊 = =||再做优化，判断条件减少（之前还不明确），也无需三目符号（之前是多此一举），代码如下：
```java
public class Solution {
    public boolean canJump(int[] nums) {
        if( nums == null ){
            return false;
        }
        int rightPlace = nums.length - 1;
        for( int i = nums.length - 1; i >= 0; --i){
            if( nums[i] + i >= rightPlace){
                rightPlace = i;
            }
        }
        return rightPlace == 0;
    }
}
```

##### Discuss 讨论区的其他方法

###### 正推
其上的方法都是逆向思维，所谓的倒推。
讨论区的方法一般是正推，从起始位置开始计算能够跳跃的最远距离。也就是说，能跳跃的范围不断地扩大，直至无法扩大，看是否超越了数组的边界。
```java
public class Solution {
    public boolean canJump(int[] nums) {
        // 上次的是自己想的方法，这个是参考的Discuss的方法。（再次提交）
        if( nums == null || nums.length == 0){
            return false;
        }
        int maxDistance = nums[0];
        int needToReach = nums.length - 1;
        for( int i = 0; i <= maxDistance; ++i){
            maxDistance =  Math.max(maxDistance, nums[i] + i);
            if( maxDistance >= needToReach){
                return true;
            }
        }
        return false;
    }
}
```
思路很好，时间为8s。

还有一种根据Jump的值为0来判断的，估计是遇见0的话，看是否可以跳出0的范围？感觉方法思想一般，具体未研究。

LeetCode出了分析，详细版的在这里
https://leetcode.com/articles/jump-game/，思路很像哎，惊呆了！