---
title: LeetCode 8 String to Integer (atoi)
date: 2016-04-12 19:44:19
tags: [LeetCode]
categories: [OJ, LeetCode, Java]

---
<blockquote class="blockquote-center">
<div style = "padding-left: 50px; text-align: left">
**
Implement atoi to convert a string to an integer.

Hint: Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.

Notes: It is intended for this problem to be specified vaguely (ie, no given input specs). You are responsible to gather all the input requirements up front.
**
</div>

</blockquote>

自己实现一个String型转Integer型的功能(C语言的atoi函数)

<!--more-->

#### 思路:
**一般的情况**：一个String型先转换成Char型数组，再从第一个数字开始读取，每次将原来的数乘以10，加上当前的读取值。
**特殊情况**：
题目给的 Requirements for atoi:
The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.
The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.
If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.
If no valid conversion could be performed, a zero value is returned. If the correct value is out of the range of representable values, INT_MAX (2147483647) or INT_MIN (-2147483648) is returned.
+ 在真正的int型数字前可能有空白字符
+ 数字前允许有‘+’(正号)和‘-’(负号)
+ 在解析过程中，尽可能的转化尽可能多的数字，直到遇到不是数字的字符。
+ 可能遇到String为空/只有空白字符/第一个非空白字符的不是数字，此时输出零。
+ 如果数值大于int型的最大值，则输出最大值；如果数值小于int型最小值，则输出最小值。(这里判断略复杂，所以会耗时，要是提前做一个简单的判断，譬如读取的位数还没有大于什么的时候就不判断是否overflow)
+ 对于overflow的情况，用long型先做结果存储，再比较与MAX_VALUE与MIN_VALUE的大小，比较方便。
#### 代码:
```java
public class Solution {
    public int myAtoi(String str) {
        if(str == null || str.length() == 0 ){
            return 0;
        }
        int ret = 0;
        int i = 0;
        int sign = 1;
        char[] result = str.toCharArray();
        while( result[i] == ' '){
            ++i;
        }
        if(result[i] == '-'){
            sign = -1;
            ++i;
        }
        else if( result[i] == '+'){
            ++i;
        }
        int start = i;
        for( ; i <str.length(); ++i){
            if( result[i] > '9' || result[i] < '0'){
                break;
            }
            // if( i - start < 9 ){
            //    ret = ret*10 + result[i] - '0';
            //    continue;
            // }
            if( (ret > (Integer.MAX_VALUE / 10) 
               || ret == (Integer.MAX_VALUE / 10) && (result[i] - '0') > (Integer.MAX_VALUE % 10)  )
               && sign == 1){
                return Integer.MAX_VALUE;
            }
            else if((ret > (Integer.MAX_VALUE / 10) 
               || ret == (Integer.MAX_VALUE / 10) && (result[i] - '0') > (Integer.MAX_VALUE % 10 + 1)  )
                && sign == -1){
                return Integer.MIN_VALUE;
            }
            ret = ret*10 + result[i] - '0';
        }
        ret = ret*sign;
        return ret;
    }
}
```
```
// 去掉注释就是 3 ms, 不去掉就是 4 ms ...
// ret用long型保存，将for()循环用以下代码替代，也可以到 3 ms。记得最终结果要强转类型。
        for(; i <str.length(); ++i){
            if( result[i] > '9' || result[i] < '0'){
                break;
            }
            ret = ret * 10 + result[i] - '0';
            if( sign == 1 && ret > Integer.MAX_VALUE ){
                return Integer.MAX_VALUE;
            }
            else if( sign == -1 && ret*sign < Integer.MIN_VALUE){
                return Integer.MIN_VALUE;
            }
        }
        ret = ret*sign;
```