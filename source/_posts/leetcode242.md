---
title: LeetCode 242 Valid Anagram
date: 2016-03-12 16:04:06
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---

<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
 **
 Given two strings s and t, write a function to determine if t is an anagram of s.
For example,
s = "anagram", t = "nagaram", return true.
s = "rat", t = "car", return false.
Note:
You may assume the string contains only lowercase alphabets.
Follow up:
What if the inputs contain unicode characters? How would you adapt your solution to such case?
**
</div>

</blockquote>

查看两个字符串是否由一样的字符组成(数量&种类)。

<!--more-->

#### 思路:
这里只考虑了ASCII的。
+ 一开始调用了getBytes()转换数据类型，后换成了toCharArray()。后者效率要高很多。
+ 字符数s中每遇一个字符，相应的加一；字符串t中每遇一个字符，相应的减一，最后比较存储数量的数组的大小是否为0。这要比两者加一，再比较是否相应相等要快一点。
+ 看了别人3ms的代码，它把两者分开了，字符串s的遍历为第一轮，字符串t的遍历为第二轮。第二轮中一遇到数量数组的值小于0，则退出。相当于不需要完全遍历字符串t，相较而言快了1ms。
    
#### 代码：
```java
public class Solution {
    public boolean isAnagram(String s, String t) {
        if( s.length() != t.length()){
            return false;
        }
        char[] sChar = s.toCharArray();
        char[] tChar = t.toCharArray();
        int max_char = (int)('z');
        int[] sNum = new int[max_char+1];
        // int[] tNum = new int[max_char+1];
        boolean sign = true;
        for(int i = 0; i < s.length(); i++){
            sNum[(int)(sChar[i])]++;
            sNum[(int)(tChar[i])]--;
            // tNum[(int)(tChar[i])]++;
        }
        for(int i = 0; i < max_char; i++){
            if( sNum[i] != 0 ){
                sign = false;
                break;
            }
        }
        return sign;
    }
}
```