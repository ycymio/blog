---
title: LeetCode 11 Container With Most Water
date: 2016-04-06 15:42:51
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **
Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.
Note: You may not slant the container.
**
</div>

</blockquote>

给定n个非负整数a1, a2, ..., an，每个代表一个坐标点(i, ai)，以(i, ai)和(i, 0)为顶点绘画n列线段。找到两个线段，它们之间形成一个容器，这个容器可以装最大容量的水。

<!--more-->

#### 思路：
Tag: Array, Two Pointers
关于Two Pointers的题，考虑x项最大值，即(1, a1)和(n, an)。
初始的两个指针start, finish分别指向这两个点，而它现在的容量为**底(finish-start)乘以高(height[start]>height[finish]?height[finish]:height[start])**，高是取两条线段的短线段。而要求最大容量，而底最大的为(an-a1)，如果要比此时的容量大，只可能在高的值更大的时候。所以移动指针(++start或者--finish)，移动哪一个指针取决于哪一个指针指向的线段更短，每移动一次判断一次此时的容量，直到两个指针相遇。

#### 优化：
因为乘法很耗时间，所以尽量判断也不通过乘法判断。所以多加一个判断，做一个base基值为当前值，之后比这个值高的才去做乘法判断。

#### 代码：
```java
public class Solution {
    public int maxArea(int[] height) {
        if( height.length == 0)   return 0;
        int start = 0;
        int finish = height.length-1;
        int max = 0;
        while( start < finish ){
            int tmp = (finish-start)*(height[start]>height[finish]?height[finish]:height[start]);
            if( tmp > max ){
                max = tmp;
            }
            if( height[start] > height[finish]){
                int base = height[finish];
                while( height[--finish] < base );
            }
            else{
                int base = height[start];
                while( height[++start] < base );
            }
        }
        return max;
    }
}
```