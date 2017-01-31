---
title: OpenDaylight的AD-SAL何时进行master/slave判断
tags:
  - OpenDaylight
  - Laboratory
categories:
  - Laboratory
  - OpenDaylight
date: 2016-07-07 17:12:30

---

在研究OpenDaylight的AD-SAL中ConnectionManager的实现部分，看到了使用isLocal()对当前控制器权限判断。OpenFlow很多机制的细节都不是很清楚，实验中追踪后发现OpenDaylight的AD-SAL是在下发流表前进行权限检查的，就好奇何种情况会去区分master/slave的权限。

<!--more-->

以下列出了调用ConnectionManager.java中isLocal()函数的类及相应函数:
#### package为org.opendaylight.controller.sal.connection.implementation.internal
ConnectionService类中函数isLocal(Node):
ConnectionService类也新建了一个函数isLocal()，实现的功能与ConnecionManager的isLocal()类似，是SAL层的一个封装。

#### package为org.opendaylight.controller.protocol_plugin.openflow.internal
#####  FlowProgrammerService中的调用
主要操作：同步/异步增删改流表，同步/异步下发Barrier消息前，判断权限。在调用isLocal()时下发流表的Node与Flow都已经确定，只差下发流表的检查和动作了。
涉及函数：
- addFlow(Node, Flow): 下发流表前
- addFlowAsync(Node, Flow, long): 异步下发流表前
- modifyFlow(Node, Flow, Flow): 修改流表前
- modifyFlowAsync(Node, Flow, Flow, long): 异步修改流表前
- removeAllFlows(Node): 删除所有流表前
- removeFlow(Node, Flow): 删除流表前
- removeFlowAsync(Node, Flow, long): 异步删除流表前
- asyncSendBarrierMessage(Node): 异步发送Barrier消息前
- syncSendBarrierMessage(Node): 同步发送Barrier消息前

##### FlowProgrammerNotifier中的调用
主要操作：流表失效或流表下发错误时通知SAL层前，判断权限。
涉及函数：
- flowErrorReported(Node, long, Object): 通知SAL前执行isLocal()。flowErrorReported()函数的作用是当收到交换机发送的之前下发的流表的错误信息时通知SAL。
- flowRemoved(Node, Flow): 通知SAL前执行isLocal()。flowRemoved()函数的作用是当某节点的流表移除时通知SAL，这种通知只有在流表被设置为idle timeout(被删除后预留的一段时间内没有包匹配该流)或hard timeout(直接删除，没有预留时间)时有效。

##### InventoryServiceShim中的调用
该类不太熟悉，不明白Inventory以及Shim是何用，原注解为:  The class describes a shim layer that bridges inventory events from Openflow core to various listeners. The notifications are filtered based on container configurations. 以后继续...但是这里是添加/删除交换机时调用到的地方，也就是说，在添加删除交换机时，还是进行了权限判断，而不是所有控制器都会处理该部分信息，这也保证了在只有一台主控制器时控制器的同步不会引起冲突。
涉及函数：
- addNode(ISwitch): 判断是否是第一次连接该交换机，如果是并且有权限管理(是master)，则删除交换机存在的所有流表。之后调用了notifyInventoryShimListener()。
- notifyInventoryShimListener(Node, UpdateType, Set<Property>): 通知所有的内部和外部listener。这里在通知其他containers有关交换机连接情况前，判断是否有权限管理。下同。
- notifyInventoryShimListener(NodeConnector, UpdateType, Set<Property>):  通知所有的内部和外部listener。
- switchAdded(ISwitch): 当一个交换机连到控制器时调用该函数，但此时检查是否是master，并没有起实质作用，调用了notifyInventoryShimListener()和addNode()。

这里特地追溯了一下关于addNode何时被调用，调用关系如下：
InventoryServiceShim: addNode(ISwitch)
-> InventoryServiceShim: switchAdded(ISwitch)
-> Controller: notifySwitchAdded(ISwitch)
-> Controller: run() 中取switchEvents优先队列中的值后处理，之后一个是放入优先队列的函数。
-> Controller: addSwitchEvent(SwitchEvent)
-> Controller: takeSwitchEventAdd(ISwitch)
-> SwitchHandler: reportSwitchStateChange(boolean)
-> SwitchHandler: processFeaturesReply(OFFeaturesReply)
-> SwitchHandler: handleMessages() 这个是开始分析OF类型的地方

##### DiscoveryService中的调用
该类主要服务于neighbor discovery服务。这个类也不太熟悉。应该是负责链路发现，因为收到LLDP后和发送LLDP都涉及到了该类。
涉及函数：
- checkTimeout(): 超时检查，在加入transmitQ前判断。
- doDiscovery(): 在加入transmitQ前判断。
- receiveDataPacket(RawPacket): 处理LLDP包前判断。
- sendDiscoveryPacket(NodeConnector, RawPacket): 发送discovery packet（LLDP）前检查，发送的RawPacket已构造完成。

##### ReadService中的调用
主要操作: 对交换机硬件信息的统计，包括交换机、交换机缓存的流表与statistics信息。（debug可知: 某结点断开主要由InventoryServiceShim类管理，但该模块会定期被调用Update系列函数）
涉及函数:
(以下都是从交换机硬件读取信息，可能有些表述的不明显，但都是一个意思)
- getTransmitRate(NodeConnector): 读取某节点的平均传输速率前
- readDescription(Node, boolean): 读取某网络节点的描述前
- readFlow(Node, Flow, boolean): 读取某交换机硬件上的某个流表前
- readAllFlow(Node, boolean): 读取某交换机硬件上的所有流表前
- readNodeConnector(NodeConnector, boolean): 读取某网络节点的某NodeConnector的数据前
- readAllNodeConnector(Node, boolean): 读取某网络节点的所有的NodeConnector的数据前
- readNodeTable(NodeTable, boolean): 读取某节点的某项table statistics前
- readAllNodeTable(Node, boolean): 读取某节点的所有table statistics前
- nodeConnectorStatisticsUpdated(Node, List<NodeConnectorStatistics>): 通知(猜测是向SAL层通知)某交换机硬件层面结点有更新前
- nodeDescriptionStatisticsUpdated(Node, NodeDescription): 通知(猜测是向SAL层通知)某交换机硬件层面的流表视图有更新前
- nodeFlowStatisticsUpdated(Node, List<FlowOnNode>): 通知(猜测是向SAL层通知)某交换机硬件的流表视图有更新前（与nodeDescriptionStatisticsUpdated()函数的区别并不了解）
- nodeTableStatisticsUpdated(Node, List<NodeTableStatistics>): 通知(猜测是向SAL层通知)某交换机硬件层面结点表格有更新前

debug时记录了一下updated的几个函数的参数内容:
+ TableStatistics的其中一项:[NodeTableStats[tableId = OF|0@OF|00:00:00:00:00:00:00:02, activeCount = 15, lookupCount = 792, matchedCount = 769, maximumEntries = 1000000]
+ NodeConnectorState: [NodeConnectorStats[portNumber = OF|2@OF|00:00:00:00:00:00:00:01, receivePackets = 407, transmitPackets = 392, receiveBytes = 55688, transmitBytes = 53922, receiveDrops = 0, transmitDrops = 0, receiveErrors = 0, transmitErrors = 0, receiveFrameError = 0, receiveOverRunError = 0, receiveCrcError = 0, collisionCount = 0], NodeConnectorStats[portNumber = SW@OF|00:00:00:00:00:00:00:01, receivePackets = 0, transmitPackets = 761, receiveBytes = 0, transmitBytes = 105970, receiveDrops = 0, transmitDrops = 0, receiveErrors = 0, transmitErrors = 0, receiveFrameError = 0, receiveOverRunError = 0, receiveCrcError = 0, collisionCount = 0], NodeConnectorStats[portNumber = OF|1@OF|00:00:00:00:00:00:00:01, receivePackets = 396, transmitPackets = 386, receiveBytes = 54512, transmitBytes = 53546, receiveDrops = 0, transmitDrops = 0, receiveErrors = 0, transmitErrors = 0, receiveFrameError = 0, receiveOverRunError = 0, receiveCrcError = 0, collisionCount = 0]]
+ Description: HwDescription[manufacturer=Nicira Networks, Inc., hardware=Open vSwitch, software=1.4.6, serialNumber=, description=None]

##### DataPacketMuxDemux中的调用
主要操作: 收到/发送OF Message时都会检查权限。(应该仅限于PACKET_IN会被调用该函数，switch的增删改等操作不会触发调用)
涉及函数: 
- receive(ISwitch, OFMessage): 收到一个OF Message后调用，之后再发送给监听data packet的接口。
- transmitDataPacket(RawPacket): 传输一个OF Message前调用

#####  DataPacketServices中的调用
（与DataPacketMuxDemux的区别并没有研究）
涉及函数: 
- transmitDataPacket(RawPacket): 传输一个OF Message前调用

- - -

调用getLocalityStatus(Node)函数: 
org.opendaylight.controller.sal.connection.IPluginOutConnectionService.getLocalityStatus(Node)org.opendaylight.controller.sal.connection.implementation.internal.ConnectionService.getLocalityStatus(Node)org.opendaylight.controller.protocol_plugin.openflow.internal.InventoryServiceShim.switchAdded(ISwitch): 只用该函数判断了状态不是NOT_CONNECTED。org.opendaylight.controller.protocol_plugin.openflow.core.internal.Controller.notifySwitchAdded(ISwitch)org.opendaylight.controller.protocol_plugin.openflow.internal.InventoryServiceShim.startService()