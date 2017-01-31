---
title: LeetCode总览
tags: LeetCode
categories: LeetCode
date: 2017-01-07 16:56:54

---

此为对LeetCode的一些归类和短评。
<!--more-->

### 常规LeetCode

LeetCode 20. Valid Parentheses : 括号匹配，栈
LeetCode 26. Remove Duplicates from Sorted Array : 删除重复元素(数组有序)(Easy)
LeetCode 27. Remove Element : 删除所有特定值的元素(Easy)
LeetCode 34. Search for a Range : 计算有序数组中某值的index范围，二分查找的进化版
LeetCode 42. Trapping Rain Water : 栈
LeetCode 55. Jump Game: 纯正的贪心算法，算经典题吧。
LeetCode 62. Unique Paths: 求解方块路径个数，组合数 -> 感觉深究还是有很多问题
LeetCode 63. Unique Paths II: 求解方块路径个数，有限制 -> 有时候存储空间小可以节省丢时间
LeetCode 64. Minimum Path Sum:  求解路径和最小，同L63.
LeetCode 66. Plus One: 数组表示的int类型的value加一
LeetCode 67. Add Binary: 二进制加法，**char型和int型转换**
LeetCode 71. Simplify Path : 栈
LeetCode 121. Best Time to Buy and Sell Stock : 一题不晓得如何用到DP的题 (Easy)
LeetCode 136. Single Number : 异或运算
LeetCode 137. Single Number II : 二进制数值位
LeetCode 160. Intersection of Two Linked Lists : 链表交集
LeetCode 166. Fraction to Recurring Decimal : 这类题主要是符号与overflow的情况，overflow的情况要考虑全面，用long型代替int型扩展更简易。
LeetCode 168. Excel Sheet Column Title: Excel表头数字转换，int型转char型直接强转就可以了，记得测试边界用例。
LeetCode 171. Excel Sheet Column Number: 与LeetCode 168相反，比它容易。
LeetCode 172. Factorial Trailing Zeroes: 值得一做。之前想考虑2和5的情况，不需要这么复杂，两者有包含关系的。
LeetCode 223. Rectangle Area : 看清要求什么。同时注意题目已给的约束。
LeetCode 224. Basic Calculator : 加减带括号的一题，不知道为什么乘除带括号就是String类的题了。标明是hard，但做起来不难。不够简便还需要化简的一题。
LeetCode 228. Summary Ranges: 可用二分查找
LeetCode 238. Product of Array Except Self: 特殊问题，将问题简单化
LeetCode 263. Ugly Number: 判断某数的因子是否只有2, 3, 5的倍数(Easy)
LeetCode 268. Missing Number : 异或运算/不变量
LeetCode 273. Integer to English Words : 又是一题标明hard，但不难（难道歪果仁和我们对难度的感受不同？），读英文数字。用StringBuffer没有String直接做快，各有千秋吧，毕竟要生成很多的String（也不多...）还要回收。判断条件的考虑仍欠缺。用trim()删除最后的空格也是一种方法，但这种方法代码行数减少，用的时间倒是多了。
LeetCode 283. Move Zeroes : 数组移动(Easy)
LeetCode 287. Find the Duplicate Number : 单链表中有环，求环起点
LeetCode 289. Game of Life : 调用自身的资源进行记录，数组
LeetCode 326. Power of Three : 特别的题，思路独特，值得一做。对于每个素数都适用。就是最大值需要自己先求一下。 
LeetCode 349. Intersection of Two Arrays : 数组交集 
LeetCode 350. Intersection of Two Arrays II : 数组交集
LeetCode 392. Is Subsequence : 倒也不像贪心算法、动态规划一类，更偏向two pointers 的类型，注意String类中charAt()和indexOf()时间复杂度的比较。
LeetCode 406. Queue Reconstruction by Height : 贪心算法，正常难度的题
LeetCode 415. Add Strings : 又是条加法题，倒是用到了String的charAt()和StringBuffer的reverse()，在int转String时用的是Integer.toString(i)。
LeetCode 435. Non-overlapping Intervals : 贪心算法，activity-selection problem.
LeetCode 441. Arranging Coins : 蠢蠢的求二次方程的解，主要需要考虑的问题是越界，如果用long型强转会比较方便，同时，做整数运算也会比小数运算效率高。
LeetCode 455. Assign Cookies : 贪心算法，初级题

### LeetCode Contest

LeetCode Weekly Contest 11:
LeetCode 434. Number of Segments in a String: 字符串分割
LeetCode 469. Convex Polygon(unfinished): 判断图形是否是凸多边型
LeetCode 467. Unique Substrings in Wraparound String : 子字符串，分清子字符串的作用
LeetCode 466. Count The Repetitions(unfinished): 子字符串和字符串包含关系

Note:
区分Long和long等，一个是引用对象，一个是基本类型。前者是无法进行强转的。
记得数组后写index....

LeetCode Weekly Contest 12:
LeetCode 475. Heaters: 两个数组及其距离关系
LeetCode 468. Validate IP Address: 字符串格式筛选
LeetCode 474. Ones and Zeroes(unfinished): skyline查询算法(?)
LeetCode 471. Encode String with Shortest Length(unfinished)

LeetCode Weekly Contest 13:
LeetCode 461. Hamming Distance: 汉明距离，位运算
LeetCode 477. Total Hamming Distance: 汉明距离，位运算
LeetCode 473. Matchsticks to Square(unfinished): 正方形摆火柴，和运算，递归
LeetCode 472. Concatenated Words(unfinished): 字符串问题

LeetCode Weekly Contest 15:
LeetCode 485. Max Consecutive Ones: 计算连续的1的最长长度
LeetCode 487. Max Consecutive Ones II: 在可以跳过一个0的情况下计算连续的1的最长长度
LeetCode 488. Zuma Game: 基于祖玛游戏设计的题目

### Unsolved Problems
LeetCode 309. Best Time to Buy and Sell Stock with Cooldown: 很费神的写了DP的方法...然而并不可行，最后一个测试样例超时。（好想看到长度长的直接返回啊妹的），写的复杂度为O(n<sup>3</sup>)


注: 
有些Easy的题忘记标记了。

常用易忘函数:
System类:
```java
public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
```