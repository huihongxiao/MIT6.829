---
description: Switching
---

# L1-Packet Switching

英文原文：[https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-829-computer-networks-fall-2002/lecture-notes/L1PacketSwitch.pdf](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-829-computer-networks-fall-2002/lecture-notes/L1PacketSwitch.pdf)

## Overview

这节课程会讨论使用不同的方式，将相同类型的链路互联在一起，构成一个简单的网络。这里，我们会使用一个叫做交换机（Switch）的设备，并讨论几种不同的交换方式来将数据在不同的网络之间移动。我们会关注在“packet switching”，并研究它是如何工作的。

在你继续阅读之前，你需要熟悉[L0](l0-background-single-link-communication.md#overview)中的内容。

## 1. 互联

单链路网络的有限能力 --- 包括了物理距离，连接的主机数，可承载的流量，使得我们考虑将多个单链路媒介互联在一起，以构成更大的计算机网络。在这一部分，我们将会看到多种互联技术，将同质化的网络链路连接起来。

这里互联的关键组件是Switch，这是一个特殊的计算机，它上面运行了软件使得它可以接收来自链路的frame（或者packet），处理它们，并且再将它们转发到一个或者多个其他链路上。链路们物理连接在交换机的attachment port或者switch port上（这里通常简称为端口/port，尤其当上下文是清晰的时候）。并且通过这种方式构建的网络通常被称为“receive-and-forward”网络。

> 有些人会称之为“store-and-forward”网络，但是这里我们使用一种更精确的词语。我们会称呼其他网络，例如电子邮件网络，为“store-and-forward”网络。

因为一个特定的物理链路通常会被不同计算机之间的多个并发的网络会话共享，所以交换机的基本功能就是将不同的计算机之间多个会话涉及到的数据frame进行多路复用。

过去，有两类激进的技术被开发来实现这个功能。第一种被电话网络所使用，被称为电路交换（Circuit Switching）。第二种，被例如互联网的网络使用，被称为分组交换（Packet Switching）。它们之间的关键区别在于，在Circuit Switching网络中，frame不必带有任何特殊的信息来告诉交换机如何转发数据，而在Packet Switching则需要。

## 2. Circuit Switching

Circuit Switching网络中传输信息通常需要两个阶段：首先是设置阶段，在源和目的之间路径上的每一个交换机上配置好特定的状态；其次是传输阶段，这时会实际传输frame。因为frame里面并不会包含转发信息，所以在设置阶段需要将frame的转发设置好。

一种常用的实现Circuit Switching的方法是时分复用（time-division multiplexing，TDM）。这里，连接到交换机的一个链路的物理容量C（bits/s），被逻辑上分成了N个虚拟的通道，这样每个通道分到的C/N bits/s足够用来为每个信息交互会话（例如连个终端之间的电话通话）。这里将每个独立传输会话的带宽称为R。现在假设我们限制每个frame为固定的大小---s bit。那么交换机通过这种方式实现时分复用：可以先将链路的容量分成若干个长度为s/C的时间单元，再将第i个时间单元分配给第i个传输会话。

> 严格来说，是分配给第（i mod N）个会话。

可以很容易看出来，这种方法给每个会话提供了需要的R bits/s，因为每个会话可以经过Ns/C秒，发送了s bits，用数据量除以时间等于C/N = R bits/s。

因此，每一个数据frame可以简单的通过使用它到达交换机的时间片来进行传输。因此，在Circuit Switching的设置阶段，需要为即将到来的数据传输关联一个通道，通过将第i个时间片分配给第i个传输通道。发送端计算机只会在它们在设置阶段分配的时间片中发送数据frame。

其他实现Circuit Switching的方式还包括了：波分复用（wavelength division multiplexing，WDM）；频分复用（frequency division multiplexing，FDM）和码分复用（code division multiplexing， CDM），后两者在一些蜂窝无线网络中有应用。

Circuit Switching在一个负载一致的网络中是有意义的。负载一致是指：所有的信息传递使用相同的容量，每一次传输都使用一样（或者近乎一样）的比特率。负载一致最优说服力的例子就是电话，并且现在大部分电话网络也是这样设计的（电话网络使用Circuit Switching的另一个原因是历史原因，因为Circuit Switching比Packet Switching要发明的早得多）。

但是，如果负载不一致（不同传输使用不同的比特率），Circuit Switching就会浪费链路带宽。因为大量的数据传输应用程序具有负载不一致的特征，我们应该使用其他链路分享策略。Packet Switching就是一种通用的提升这种场景性能的方法。

## 3. Packet Switching

最好的客服上面低效率的方法是允许任意的发送者在任意时间发送数据，同时又允许链路被共享。Packet Switching就是这样一种方法，并且它使用了一种非常简单的思想：为每个frame增加一些特定的额外信息来告诉交换机如何转发自己。这些额外的信息通常被添加在叫做header的地方。Packet Switching有几种不同的形式，区别在于header中的信息以及交换机需要什么样的信息来执行转发。

最“直接”的Packet Switching形式时使用数据电报作为分片的单位，并在header中包含目的地址。header中的目的地址唯一的标识了数据的目的地，每个交换机可以用这个目的地完成数据电报的转发。第二种Packet Switching的形式是源路由（source routing），它会在header中包含完整的交换机序列，或者数据电报可以到达目的地的完整的路径信息。现在每个交换机都有一个简单的转发决策，假设数电报的发送端提供了正确的信息。第三种Packet Switching的形式结合了Circuit Switching和Packet Switching，并且使用了一种叫做Virtual Circuit的思想。尽管这种方式包含了Circuit Switching中的设置阶段，因为它使用了header，我们还是将它归类为Packet Switching技术。接下来我们更具体的看这里的每一个技术。

### 3.1 Datagram routing

在Datagram routing中，发送端传输的Datagram会将目的地的底子包含在header中，datagram中通常也会包含发送端的地址，这样接收端可以将信息返回给发送端。Switch的工作就是使用目的地址作为key在一个叫做routing table的数据结构（或者是forwarding table，这两者之间的区别有的时候还挺重要，并且会在课程的后面变得明显）中进行查找，查找的结果就是一个输出端口用来向目的地转发packet。

尽管转发就是在一个数据结构中进行相对简单的查找，难点在于如何如何获取routing table中的条目。这是通过一个使用了路由协议的后台程序完成，这个程序通常以分布式的方式在交换机中实现。理论上和实际中有几种类型的路由协议（某些路由协议只存在于理论中），我们在后面的课程中会学习集中。目前为止，了解到运行路由协议是为了获取网络中每一个目的地址的路由（路径）就足够了。

在datagram网络中实现了这一小节描述功能的switch通常被称为router。使用Internet Protocol（IP）来在互联网上转发和路由packet就是datagram routing的一个例子。
