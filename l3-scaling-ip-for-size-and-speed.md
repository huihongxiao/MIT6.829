# L3 - Scaling IP for Size and Speed

英文原文：[https://ocw.mit.edu/courses/6-829-computer-networks-fall-2002/c6d11b486a590e067ee67fb3b284fc0f\_L3ScalingIP.pdf](https://ocw.mit.edu/courses/6-829-computer-networks-fall-2002/c6d11b486a590e067ee67fb3b284fc0f\_L3ScalingIP.pdf)

## Overview

这节课会介绍互联网网络层使用的一些技术，这些技术使得互联网可以扩展到数百万个网络并连接数以亿计的计算机。

## 1. Address Structure

互联网能达到如此规模的根本原因在于它的网络层使用了拓扑化的地址（topological addressing）。以太网地址与位置无关，并且总是标识一个网卡，不论这个网卡连接在网络的什么位置。而一个网卡的IP地址却与其在网络拓扑中的位置强相关。这使得IP地址的路由可以在路由器的转发表中汇聚，并且路由可以进一步汇总，通过参与到互联网路由协议的路由器与其他网络进行交换。如果没有这种激进的汇聚汇总，是不可能达成大规模系统。事实上，按照目前互联网上使用的技术，挑战也是很大。这节课我们会讨论这些技术中最重要的一部分，并解释它们如何工作。

因为一个IP地址表明了其在网络拓扑中的位置，互联网的网络层可以看成是一个经典的区域路由（area routing）来大规模的实现IP转发路径。

