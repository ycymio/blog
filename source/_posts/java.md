---
title: Java细节
date: 2016-03-06 14:19:21
tags: Java
categories: Java

---
- - -
>#### 报错 error: for-each not applicable to expression type:
>原因：for-each循环仅应用于实现了Iterable接口的Java Array和Collection类等中。

- - -

>#### Java JDK 源码在MAC的位置：
>/Library/Java/JavaVirtualMachines/jdk1.7.0_71.jdk/Contents/Home/src.zip 

- - -

>#### length与length():
>length -> 数组
>length() -> String等
>size() -> 容器类

- - -

>#### transient 
>Java语言的关键字，用来表示一个域不是该对象串行化的一部分。当一个对象被串行化的时候，transient型变量的值不包括在串行化的表示中，然而非transient型的变量是被包括进去的。
>如：
>```java
    class A implements Serializable {
        private String name;
        transient private String address;
    }
```
>那么你在串行化（I/O流等）A类时 给它的name和address属性赋值，那么你在提取A时，拿到了name属性，但是却拿不到address属性。

- - -

>#### 一些效率的提高问题
for的效率比同样效果的while的高
for的效率一般比同样效果的foreach高
多余的判断的省略也可以提高效率
在有一系列判断时，把先可以返回的放在前面判断也可以缩短运行时间...
有时候计数可以代替标志位flag，譬如LeetCode 414. Third Maximum Number
等式合并效率比分开高一些，但是不利于阅读

- - -

>#### Java数组复制
可以直接调用 System.arraycopy(); (据说速度最快? 不晓得原因，但很多复制最终都调用了它)
System类：public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);
src复制给dest，Pos -> Position 

- - -

>#### Binary Search
二分查找可以用迭代，代替递归方法
时间复杂度: O(log<sub>2<sub>n)
最好的时间复杂度: O(1)

- - -

>#### i++与++i
如果是内置类型如int单语句i++和++i没区别;
如果i是自定义的类型重载的后置++和前置++，有区别。
前置比后置效率高.
只有在必要时才使用后置操作符。
因为前置操作需要做的工作更少，只需要加1后返回加1后的结果即可。而后置操作符则必须先保存操作数原来的值，以便返回未加1之前的值作为操作的结果。对于int对象和指针，编译器可优化掉这项额外工作。但是对于更多的复杂迭代器类型，这种额外工作可能会花费更大代价。因此，养成使用前置操作这个好习惯，就不必担心性能差异的问题。

- - -

>#### 对于 *2<sup>n</sup> 与 /2<sup>n</sup>
使用移位运算往往更快。

- - -

>#### String的toLowerCase()中的scan用法
```java
 /* Now check if there are any characters that need to be changed. */
        scan: {
            for (firstUpper = 0 ; firstUpper < len; ) {
                char c = value[firstUpper];
                if ((c >= Character.MIN_HIGH_SURROGATE)
                        && (c <= Character.MAX_HIGH_SURROGATE)) {
                    int supplChar = codePointAt(firstUpper);
                    if (supplChar != Character.toLowerCase(supplChar)) {
                        break scan;
                    }
                    firstUpper += Character.charCount(supplChar);
                } else {
                    if (c != Character.toLowerCase(c)) {
                        break scan;
                    }
                    firstUpper++;
                }
            }
            return this;
        }
```
这是带标签的break语句，用来跳出多重循环用的。
scan仅仅是一个标签名，与"break scan;"这一句中的scan对应。
关于Java的标签:
(1) 简单的一个continue会退回最内层循环的开头（顶部），并继续执行。
(2) 带有标签的continue会到达标签的位置，并重新进入紧接在那个标签后面的循环
(3) break会中断当前循环，并移离当前标签的末尾。
(4) 带标签的break会中断当前循环，并移离由那个标签指示的循环的末尾。

- - -

>#### 关于判断语句太长的问题
判断语句太长，并且要多次相同判断还不可以用循环的，直接写成函数判断吧。(虽然可能影响速度?

>#### JAVA三个移位运算符 << 、>> 、>>>
">> 右移,高位补符号位" 这里右移一位表示除2
">>> 无符号右移,高位补0" 与>>类似
"<< 左移" 左移一位表示乘2，二位就表示4，就是2的n次方

>#### Java中instanceof和isInstance()
java.lang.Class.isInstance()确定如果指定的对象是与这个类表示的对象赋值兼容的。它是Java语言的instanceof运算符的动态等效。主要可以用于以下表示：
```java
String x = "hello";
String m = "world";
if (m.getClass().isInstance(x)){
}
```

