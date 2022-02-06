# L2-The Internetworking Problem

英文原文：[https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-829-computer-networks-fall-2002/lecture-notes/L2Internetworking.pdf](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-829-computer-networks-fall-2002/lecture-notes/L2Internetworking.pdf)

## Overview

这节课会来看将不同网络互联在一起会有哪些问题。我们会学习互联网的设计原则，IP，互联网尽力而为（best-effort）的服务模型，它的设计和协议准则。

之后我们讨论IP和TCP的区别，并学习TCP是如何完成可靠数据传输。我们会看TCP的ACK机制，以及两种形式的丢包重传：时间驱动（timer-driven）和数据驱动（data-driven）。前者依赖于预估连接的round-trip time（RTT）来设置超时，而后者依赖于接收到数据流中更靠后的包来避免等待超时。数据驱动重传的例子包括了快速重传（fast retransmission）和选择性确认（selective acknowledgment SACK）。

## 1. 网络互联的问题：许多不同类型的网络

上一节课，我们讨论了packet switching的原理，并且设计了一种方案来将LAN连接在一起。这里的核心在于一个叫做switch的设备，它会在不同的LAN之间转发packet。

尽管这样的网络可以工作，并且也很常见，它并不能用来构建一个全球的数据网络基础设施，它有两个主要的缺陷：

1. 它不能容纳异构性
2. 它并不能扩展到大规模网络

以上每一点都值得讨论。首先以太网绝不是世界上唯一的链路层技术，实际上，在以太网之前，packet在其他诸如无线射频，卫星信号，电话线等链路上传输。其次，以太网也通常不适用于几百公里的长距离传输。各种新的数据链路层技术一直在出现，并且会持续出现，而我们的目标就是确保任何新的数据链路层技术都可以接入到全球数据网络基础设施中。这就是容纳网络异构性的目标。

我们在[L1](l1-packet-switching.md#4.-yi-ge-li-zi-lan-switching)学习的LAN switching要求switch存储网络中每个host（实际上是每个网卡）的状态，如果switch中没有目的地址的状态，它会退而求其次将packet广播到整个网络中的所有链路上。这个在中等规模的主机数和链路数上都无法工作。

所以，网络互联的问题或许可以这样描述：设计个可扩展的网络基础设施，它能互联多个不同的小的网络，可以差异很大的网络上的主机之间能传递packet。

有关网络的差异，包含以下例子：

* **网络地址**。每个网络可能有自己地址格式。例如，以太网使用6个字节作为地址，而电话使用10位数字作为地址。
* **带宽和延时**。带宽可能会从几个bit/s到几个Gb/s，中间的跨度会有几个量级。类似的，延时也可能从几个微秒到几秒。
* **包大小**。不同网络的最大包尺寸可能会不同。
* **丢包率**。不同网络的丢包率和丢包模式都不相同。
* **路由模式**。不同网络的路由模式可能不同。例如射频网络的路由协议与有线网的路由协议就不一样。

从规模角度考虑，我们的目标就是尽可能的减少由switch维护的状态，和减少由控制流量消耗的带宽（例如路由信息，定期广播的消息等）

