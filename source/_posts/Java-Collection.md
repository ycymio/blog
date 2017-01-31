---
title: Iterable接口与Collection接口
tags:
  - Java
categories:
  - Java
  - JDK
date: 2017-01-31 12:51:07
---


这里主要概述下Iterable接口以及Collection接口及其相应接口函数。
<!--more-->

## Iterable&lt;T&gt;接口
> 所属包 java.lang。
> 在1.5版本以后支持for-each操作。

### 方法

+ default void forEach(Consumer<? super T> action)： 循环遍历所有Iterable的元素直到所有元素都被访问或者某个操作抛出异常。（没试过）
+ Iterator<T>	iterator()： 返回类型为T的迭代器。
+ default Spliterator<T>	spliterator()： 创建一个由该Iterable描述元素的Spliterator。（没试过）

## Collection&lt;E&gt;接口
> 所属包 java.util。
> 扩展了Iterable&lt;E&gt;接口。
> Collection接口是Collection关系层次的根节点。
> 所有实现Collection接口的类，都需要实现两个构造器，创建空collection的无参构造器和创建一个与唯一参数相同元素的构造器（用于复制）。
> 如果某操作不支持，或者方法体为空，需抛出UnsupportedOperationException。如果某元素不符合要求，则抛出NullPointerException或ClassCastException。
> 每个集合需自己确定同步策略。

### 方法

+ int size()： 返回集合的元素数量（最大不超过Integer.MAX_VALUE）
+ boolean isEmpty()： 集合为空则返回true
+ boolean contains(Object o)： 如果集合包含该参数代表的元素，则返回true。一般若o为null，则若集合为空，返回true，否则返回false。
+ Iterator<E> iterator()： 返回集合元素的迭代器，除非有所设置，一般不保证遍历顺序。
+ Object[] toArray()： 返回包含所有集合元素的数组，如果有设定，则数组元素顺序需和它遍历器的遍历顺序一致。返回的数组是独立的，可随意更改，不会对集合产生影响（改元素内容也是咯？）。
+ <T> T[] toArray(T[] a)： 返回a，a包含了所有的集合元素。与toArray()类似，除了将元素保存至指定空间，如果空间不够会自动分配，空间多余则缩减。（类型指定）
假设x是一个只包含String类型的集合，则以下的代码可以将集合复制到一个新分配的String数组中。要注意的是toArray(new Object[0])与toArray()方法等同。
```Java
   String[] y = x.toArray(new String[0]);
```
+ boolean add(E e)： 确保集合包括元素e(可选操作)。如果添加成功则返回true，如果集合已存在该元素则返回false。如果不允许添加该元素应抛出异常。null是否可被添加应在相关文档中标出。
+ boolean remove(Object o)： 如果存在，则删除该元素（可选操作）。集合改变（元素被删除）则返回true。
+ boolean containsAll(Collection<?> c)： 如果包含集合c中的所有元素，则返回true。
+ boolean addAll(Collection<? extends E> c)： 添加集合c中的所有元素到当前集合(可选操作)。如果在函数执行时集合c元素有变动，则该函数行为导致的结果无法预测。集合改变则返回true。
+ boolean removeAll(Collection<?> c)： 从当前集合中移除集合c中的所有元素(可选操作)。集合改变则返回true。
+ boolean	retainAll(Collection<?> c)： 求交集，即保留当前集合与集合c中含有的相同元素(可选操作)。集合改变则返回true。
+ void clear()： 清空当前集合(可选操作)。
+ boolean equals(Object o)： 比较特定对象是否与当前对象相等。
+ int hashCode()： 返回当前集合的哈希值。

以下是Java 8新加的方法：
+ default boolean	removeIf(Predicate<? super E> filter)： 从当前集合中删除filter内的元素。如果某些元素被删除则返回true。
+ default Stream<E>	parallelStream()： Returns a possibly parallel Stream with this collection as its source.
+ default Spliterator<E>	spliterator()： Creates a Spliterator over the elements in this collection.
+ default Stream<E>	stream()： Returns a sequential Stream with this collection as its source.