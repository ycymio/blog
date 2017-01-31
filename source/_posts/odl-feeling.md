---
title: 关于OpenDaylight AD-SAL的使用感想
date: 2016-07-19 16:52:43
tags: [OpenDaylight, Laboratory]
categories: [Laboratory, OpenDaylight]

---

#### 2016-07-19
一直用的相当于是OpenDaylight氢版本，却被师兄说在用氦版本(氦版本也分之前之后的好么)...:)

最近对这个古老的版本越发的不满，很多接口设计的不好，开放性也不好，可扩展性也不好。想监听Flow Remove事件，它居然只允许一个接口监听一个Message事件。果然是初级版本。。。看它源码会有一定收获，但也挺头疼的。

#### 2016-11-08
接触新版本OpenDaylight快两个月，也大概摸了个初步。可以在OpenFlowPlugin模块上搭个集群，与Mininet连接跑跑OpenFlow 1.3。
在其中发现了些比较坑的地方。OpenDaylight其实不支持控制器的Equal状态(这里的状态指控制器与交换机之间)，它的迁移状态只有NoChange，BecomeMaster与BecomeSlave。也许是Equal状态设计起来太麻烦了吧。然而Mininet一开始默认所有连接的控制器是Equal状态。这里机制还不太清楚，难道只判断是否是Master或Slave再进行的操作？
OpenFlow 1.3中的Master与Slave至今才有了深刻的认识，有了控制器角色一说，交换机发送包的状态就不再是广播，而是有针对性的发送了，除非是equal，而equal在OpenDaylight中需要扩展。
思考下是否想扩展吧。