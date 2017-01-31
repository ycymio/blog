---
title: Android遇到的各个小问题
date: 2016-04-07 15:34:07
tags: Android
categories: Android

---

学习Android过程中遇到的 单独写博文又觉得有些奇怪 的小问题。

<!--more-->
> Android工程下没有R文件怎么办?
当你导入一个新工程的时候或者新建一个工程，发现没有R文件:
+ 右键选择你的工程，refresh
+ 在编辑器上面选择Project,再选择Clean, 右键选择你的工程，选择Android Tools,再选择Fix Project Properties。
+ 最后如果R文件还没有出来，看控制台报出的错误，一般是资源文件报错所致。