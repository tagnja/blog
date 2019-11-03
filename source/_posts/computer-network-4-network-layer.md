---
title: 深入理解计算机网络（四）：网络层
date: 2019-09-05 18:18:33
categories: 
- 计算机基础
- 计算机网络
tags: Network
---

## 介绍网络层

网络层提供了两个主机（终端）的信息传输服务。相比于应用层和传输层，它们提供的服务是一台主机的一个进程与另一台主机的一个进程之间的通信，它们是与进程绑定的，而网络层是与主机绑定的，一个主机在网络中通过 IP 地址进行唯一标识。

因特网的结构

因特网可以分为网络的边缘和网络的中心。网络的边缘是一个路由器连接着各种各样的终端设备，如电脑，手机等。网络的中心是由许许多多的路由连接起来的一个网状结构，它组成了网络的骨架，。因特网的结构如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-1-introduction-1-the-internet-structure.png" />

应用层和传输层只出现网络边缘的终端设备上。在网络的中心的路由器上没有应用层和传输层，最高层为网络层，它负责传输网络层数据。

应用层和传输层是两个终端设备上的进程之间的通信，它们只关心进程发给网络层的数据是什么样的，以及从网络层接收到的数据是什么样的。它们不管网络的结构是什么样的，数据是如何在网络中传输的。

网络层实现的功能

网络层主要实现在两个主机之间的网络通信，它要将一个主机上的数据，经过网络（多个路由器），传递给另一个主机。要实现这个功能，需要解决两个问题：1. 网络层数据如何在一个路由器节点上传输？2. 网络层数据如何选择一条最快的网络路径进行数据传输？

路由器上的数据传输是指数据包从路由器的一个输入链路接口传输到一个输出链路接口。这个功能是通过硬件实现的。而选择路径即路由，路由一般是通过软件实现的。

## 网络层的数据转发（Forwarding）

网络层的信息通信，是将网络层的数据报（Datagram）从客户端主机，经过许多个路由器的转发，最终传递到服务端主机。为了了解网络层数据是如何转发的？我们先介绍路由器的内部工作原理。然后介绍数据报转发过程中用到的 IP 协议。

### 路由器是如何工作的

<br>

#### 路由器的结构

路由器结构如下图所示。它主要由以下四个部分组成。

<img class="img-center" src="https://taogenjia.com/img/network-4-network-layer/router-architecture.png">

- 输入端口（Input Port）：输入端口执行三个步骤。第一步执行物理层的功能，终止传入的物理链路。第二步执行链路层功能，与传入链路另一侧的链路层相互操作。第三步，查询转发表，判断将数据传送到哪个输出端口。
- 交换结构（Switch Fabric）：连接输入端口和输出端口。
- 输出端口（Output Port）：接受交换结构的数据，传播这些数据到输出链路。
- 路由选择处理器（Routing Processor）：维护路由表和链路状态信息，计算路由器的转发表。

> 路由表和转发表的区别是什么？
>
> 路由表是每个路由器用来计算和维护网络拓扑结构中的最小花费路径。它每一个行记录了目标地址（Destination），网络掩码（Mask），和下一跳 IP 地址（Next Hop）等等。
>
> 转发表用来查询 IP 报文转发到哪个输出端口。

#### 路由器的工作原理

1\. 输入端口主要是实现查询报文的输出端口的功能。通过在转发表中查询匹配目标 IP 地址的前缀，从而得到对应的输出端口。如果目标地址匹配多个前缀，则使用**最长前缀匹配规则**（Longest Prefix Matching Rule）。

2\. 转换组织的实现方式有很多种。常用的三种实现方式如下：

Memory（内存）。拷贝数据包到输出端口的内存区域。缺点是多个数据包不能同时读写内存。

Bus（总线）。输入端口将数据包直接传输给输出端口。缺点是多个数据包不能同时使用 bus。

Crossbar（交叉开关矩阵）。由 2N 个bus 连接 N 个输入端口和 N 个输出端口。可以同时传输多个数据包。缺点是不能同时传输多个同一输出端口的数据包。

3\. 输出端口，从内存区域的队列中选择一个数据包出队，将使数据包封装成帧在链路上传输。

#### 路由器的等待队列和数据调度

路由器的等待队列可能发生在输入端口和输出端口。队列的长度却决于网络的负载与路由器的处理的速度。当网络的负载超过路由器的处理速度，最终会导致没有足够的内存可用，产生丢包后果。

输入端口队列中的数据包等待着通过交换结构传输到输出端口。输出端口队列中的数据包等待传输到链路上。

在输出端口通常使用调度算法，控制数据包的传输的顺序。常见的调度算法有：

- First-in-First-Out (FIFO)，先进先出算法，使用简单的规则，最先到达的数据包最先处理。
- Priority Queueing，优先队列，按优先度分为不同的队列，先处理高优先度的队列，再处理低优先度的队列。
- Round Robin and Weighted Fair Queuing (WFQ)，循环与加权公平队列，按照优先度分为不同的队列，每个队列依次循环处理。



### IP 协议和 IP 寻址

<br>

#### IPv4 协议

<br>

##### IPv4 的报文格式

IPv4 协议定义了 IP 报文的数据格式。IPv4的数据报文格式如下图所示。

<img class="img-center" src="https://taogenjia.com/img/network-4-network-layer/ipv4-datagram-format.png">

报文字段解释

- Version：占4位，指定了 IP 协议的版本。
- Header length：表示头部的长度。长度=20字节+Options的长度。
- Type of service：用于标识报文是实时应用，还是非实时应用报文。实时应用如网络电话。非实时应用如FTP。
- Datagram length：表示整个报文的长度，头部+数据域。
- Identifier，Flags，Fragmentation offset：用于 IP 报文分片。
- Time-to-live：可存活的时间。当TTL=0时，数据报需要被丢弃。
- Protocol：指出使用的传输层的协议。
- Header checksum：用于 IP 报文错误检查。
- Source IP address，Destination IP address：表示客户端和服务端主机的 IP 地址。
- Options：用于 IP 头部的扩展。一般很少使用。
- Data：数据负载。传输层数报文段或者ICMP消息。

##### IPv4 报文分片（Datagram Fragmentation）

链路层有不同的链路类型，不同的链路有不同的最大传输单元（Maximum Transmission Unit，MTU）。在链路上，一般一个网络层的 IP 报文封装一个链路层的帧。由于不同的链路有不同的 MTU，会导致一个 IP 报文在一个链路能传输，而在另一个链路由于 IP 报文长度大于该链路的 MTU，而不能在该链路传输。如，以太网链路最大传输单元为1500 bytes，而广域网链路最大传输单元为 576 bytes。

为了解决这个问题，需要用到 IP 报文分片。当 IP 报文长度大于该链路的 MTU 时，将 IP 报文分为多个分片（Fragment），每个分片封装成一个帧在链路上传输。

IP 报文分片头部设置为：每个分片设置相同的 identification number；前面的分片 flag bit 设为 1，最后一个分片 flag bit 设为 0；offset 指定分片在原始的 IP 报文中的相对位置，第一个分片 offset 设为 0。

IP 报文分片检查。路由器通过相同的 identification number 判断是一个 IP 报文分片，通过 flag 判断分片是否结束，通过 offset 知道分片的顺序。

### IP 寻址

IP 寻址（IP Addressing）是指对某个 IP 地址进行定位。其中涉及到许多网络概念，如 IP 地址，接口，子网，子网掩码等等。下面对这些概念进行简单描述。

接口（Interface）：连接主机和物理链路边界。每台主机有一个接口，路由器通常由多个接口。

IP 地址：每个接口必须有一个 IP 地址。IPv4 地址占 32 位。地址的表示为，4个十进制的数字以点分隔，如193.32.216.9。

子网（Subnet）：由一个路由器接口和多个主机（或路由器）接口互相连接的网络称为一个子网。IP 网络可以划分为很多个子网。同一个子网的所有接口具有相同的 IP 地址前缀。

子网掩码：表示一个子网的相同的 IP 地址前缀。一般形式为 a.b.c.d/x，其中 x 表示前缀的长度。如一个子网的子网掩码为 233.1.1.0/24，那么这个子网中所有的接口 IP 地址有共同的24位前缀 233.1.1...，IP 地址可以为 233.1.1.1，233.1.1.2，233.1.1.3 等等。

有类别的寻址（Classful Addressing）：网络划分中IP地址的前缀位数限制为8，16，24位。

无类域间路由（Classless Inter-Domain Routing, CIDR）：CIDR用 13-27位长的前缀取代了有类别寻址限制的3种前缀。

IP 广播地址（Broadcast Address）：IP 广播地址为 255.255.255.255，发送报文给广播地址，这个子网的所有主机都能收到这个消息。



#### 获取一组 IP 地址

一个机构或公司想要获取一组 IP 地址，构建自己的子网。需要从网络提供商（Internet Support Provider, ISP） 那里获取。如中国电信和中国移动等。

ISP 如何获得一组 IP 地址。IP 地址是被一个权威组织 ICANN （Internet Corporation for Assigned Names and Numbers）所管理的。ISP 可以从权威组织那里获取一组 IP 地址。

#### 获取一个主机的 IP 地址

一台主机想要连接互联网，需要获取一个 IP 地址。为主机分配 IP 地址可以手动配置，也可以使用DHCP（ Dynamic Host Configuration Protocol）协议来自动分配。DHCP 协议是即插即用的零配置的协议。

DHCP 协议分配IP地址的过程：

- 新接入网络的主机发送获取 IP 地址请求，通过广播 DHCP discover message。
- DHCP 服务器响应请求，返回分配的 IP 地址，子网掩码，和 IP 地址租用时间。通过广播 DHCP offer message。
- 主机发送请求确认，通过广播 DHCP request message。
- DHCP 服务器响应请求，确认请求参数。通过广播 DHCP ACK message。

### NAT

一台服务器主机需要被网络访问，优先需要拥有一个广域网 IP 地址。当服务器主机不断扩展增加，没有足够的 IP 地址时，可以通过 Network Address Translation (NAT) 可以进行 IP 地址扩展。可以将一个广域网 IP 地址，扩展为多个局域网 IP 地址。即一个广域网 IP 地址，通过映射局域网 IP 地址，可以为多台主机提供网络接入。

NAT 的工作原理是：

- 所有的主机与 NAT 路由器连接，所有的主机对于外部世界看作是单个设备和一个 IP 地址，通过这一个 IP 地址不同的端口来区分内部的主机。
- NAT 使用 NAT 转换表（NAT Translation Table） 将这个公开的 IP 地址加端口和所有内网 IP 地址加端口之间有一对一映射关系。也就是 NAT 转换表的一个记录，将公网 IP 和一个端口映射一个内网的进程。
- 外部客户端请求 NAT 内的主机，是通过请求 NAT 路由器的 IP 地址和指定端口，NAT 路由器接收到请求后，通过修改报文的目标地址为局域网地址，修改目标端口为进程的端口，将这个报文转发给内网的主机的一个进程。
- NAT 内的主机访问外部的主机，请求经过 NAT 路由器的时候，NAT 路由器修改报文的源地址为 NAT 路由器的地址，修改源端口为 NAT 路由器的一个端口。
- 所有内网中的主机不直接与外部的通信，所有的通信都是 NAT 路由器与外部进行的。NAT 通过修改报文的内容从而达到与内网的通信。

NAT 转换表如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-4-network-layer/NAT-translation-table.png">

### IPv6 协议

由于 IPv4 的地址空间快用完了。需要一个新的 IP 协议支持更多的 IP 地址，所有 IPv6 诞生了。

IPv6 相比与IPv4有哪些重大的改变：

- 扩展了地址空间。IP 地址由原来的32位变成128位。
- 支持任播 anycast address。多个主机使用相同的 IP 地址，路由器优先访问离自己最近的一个。相当于 CDN 的功能。
- 固定的40字节的报文头部。固定的报文头部使得 IP 报文可以被路由器处理得更快。
- 流标签。定义报文数据流的类型。

IPv6 的报文格式如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-4-network-layer/ipv6-datagram-format.png" />

报文字段解释

- Version：表示协议的版本。
- Traffic class：表示服务的类型。指定报文的优先度。
- Flow label：表示流的类型。实时应用还是非实时应用。
- Payload length：数据域的长度。
- Next header：指定传输层的协议类型。TCP 还是 UDP。
- Hop limit：在路由器中转发的次数。当 hot limit 为0是该报文将被丢弃。
- Source and destination address：源 IP 地址和目标 IP 地址。
- Data：数据域。

IPv6 相对于 IPv4 去除的字段

- Fragmentation：IPv6 不需要分片。当报文长度大于链路最大传输单元时，该报文直接被丢弃，而不是进行分片。
- Header checksum：不再执行错误检查。传输层和链路层的错误检查已经足够了。
- Options：IPv6 使用固定的头部，不需要可选字段。



## 网络层的路由管理（Routing）

路由表记录和维护着网络通信的最小花费路径，通过路由表使整个网络通信更加高效。路由表则是通过路由算法和路由协议得到的。这一小节主要讲述常见的路由算法和路由协议。

### 路由算法

网络结构像是一个由多个节点相互连接的图。一个图结构有 N 个节点和 E 条边组成。每一个相邻的节点称为邻居，任意两个连通的节点形成的一条路径。路由算法就是算出任意两个节点之间的最小花费路径（Least-cost path）。

路由算法可以通过很多方式实现，可以将它们进行分类：

- 集中路由算法，分散路由算法
- 静态路由算法，动态路由算法
- 负载敏感算法，负载不敏感的算法

#### Link-state (LS) 路由算法

LS 算法是一个集中式路由算法，它采用了 Dijkstra 算法计算最小花费路径。使用的方式是：每个路由器运行 LS 算法，计算与其它每个节点的最小花费路径。

LS 算法的描述

初始化过程：计算相邻的节点的路径花费。不相邻的节点初始化为无穷大。

循环过程：循环计算当源节点到目标节点（邻居的邻居节点）的最小路径，直到所有节点都经过计算。

算法伪代码如下：

```
/* 
D(v): cost of least-cost path from the source node to desination v.
c(u,v): cost of from u to directly attached neighbor v.
N: all nodes
N': subset of nodes.
Time complexity: O(n^2)
*/
Initialization:
N' = {u}
for all nodes v
	if v is a neighbor of u
		then D(v) = c(u,v)
	else D(v) = ∞

Loop
find w not in N' such that D(w) is a minimum
add w to N'
update D(v) for each neighbor v of w and not in N'
	D(v) = min(D(v), D(w) + c(w,v))
util N' = N
```

#### Distance-Vector (DV) 路由算法

DV 算法是一个异步，分散的算法。

DV 算法描述

```
Initialization:
for all destination y in N:
	Dx(y) = c(x,y) /* if y is not a neighbor then c(x,y) = ∞ */
for each neighbor w
	Dw(y) = ? for all destinations y in N
for each neighbor w
	send distance vector Dx = [Dx(y): y in N] to w

loop
	wait (until I see a link cost change to some neighbor w or until I receive a distance vector form some neighbor w)
	for each y in N:
		Dx(y) = min{c(x,v) + Dv(y)}
	if Dx(y) changed for any destination y
		send distance vector Dx = [Dx(y): y in N] to all neighbors
forever
```

#### LS 和 DV 路由算法的比较

- 消息复杂度。LS 算法需要直到每一条网络链路的花费。这需要 O(NE) 消息发送。当一个链路花费发生改变时需要把新的花费发给所有节点。DV 算法需要在所有直接连接的邻居之间交换消息。这个复杂度取决于很多因素。当一个链路花费发送改变时，新的花费将通过一个节点向和它连接的节点进行传播。
- 收敛速度。LS 是一个 O(N^2) 的算法需要 O(NE) 个消息传递。DV 算法没有明确的复杂度，可能会导致无限死循环。
- 鲁棒性。LS 算法计算是每个路由器独立的，一个路由器的计算机结果不会受其它路由器的影响。DV 算法根据所有的邻居来计算，当一个节点出现错误时，可能导致整个网络出现错误计算。

两个算法都有自己的优缺点，它们都使用在当今的因特网中。

### 路由协议

想要计算和维护任何一个路由器节点到世界范围内的其它所有路由器节点的最小花费路径，是一件很复杂的事情。主要的问题有两个方面。

- 网络规模。目前世界上有数亿的路由器节点。想要计算和存储如此多的路径信息，需要很多的计算时间和存储空间。
- 网络管理。每个国家每个地区，希望能够自己控制和管理自己的网络，以及隐藏自己内部的网络结构。

为了解决这些问题，每个地区可以通过一组路由器构建一个自治系统（Autonomous System, AS）。如今世界上存在的因特网，就是由很多个 AS 组成的网络，一个 AS 与另一个 AS 之间直接或间接地连接和沟通，就连通了整个因特网。一个 ISP（网络服务提供商） 通常会组建一个自己的网络自治系统，也有的 ISP 会组建多层级的自治系统。当今世界的因特网的结构大致如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-4-network-layer/inter-isp-network.png">

一个 AS （或者 ISP ）内部的路由管理，通过自治系统内部路由协议（Intra-Autonomous System Routing Protocol）实现，一个 AS 内的所有路由器运行相同的路由算法。常见的协议，如 OSPF 协议。

AS 之间的路由管理，通过自治系统间的路由协议（Inter-Autonomous system routing protocol）实现，每个 AS 运行相同的自治系统间的路由协议进行交流。常见的协议，如 BGP 协议。



#### ISP 内部路由管理：OSPF 协议

OSPF 协议使用 link-state 路由算法计算 ISP 内部任何两个节点之间的最小花费路径。OSPF 不是当链路发生改变时运行，而是每个路由器定期（每30分钟）向其它路由器的广播路由信息。OSPF 消息是使用 IP 报文进行负载的。

#### ISP之间的路由管理：BGP 协议

在因特网中，所有的AS运行相同的 inter-AS 路由协议称为边界网关协议（Border Gateway Protocol, BGP）。在 BGP 协议中，每个 AS 有自己的一组 IP 地址，通常是连续的有相同前缀的 IP 地址。一个 AS 通过CIDR 前缀向其它 AS 广播自己的存在。

BGP 协议提供的服务

- 一个 AS 通过 AS 邻居获取可到达的网络 IP 前缀信息。
- 提供一个最好的路径从一个 AS 到达另一个 AS。

关于 BGP 协议是如何广播一个 AS 的存在，以及如何计算最佳的AS 间的路径，这里不详细展开。详细内容可以查阅最后的参考书籍。

## 网络的管理

### ICMP 协议

通过 ICMP (Internet Control Message Protocol) 协议，主机和路由器之间可以进行交流。ICMP 报文是使用 IP 报文进行负载的，通过设置头部的 upper-layer protocol number 为 1，主机可以识别这个一个 ICMP 报文，而不是 TCP 或 UDP 报文。ICMP 报文有类型和代码表示不同的含义。如，当一个主机不可访问时，路由器会返回 ICMP 报文给客户端，报文设置为 type=3,code=1 表示目标主机不可接入。

ICMP 常见的功能

- 报告错误。
- 实现 ping 程序。
- 拥塞控制。
- 实现路由追踪程序。

### 网络管理和 SNMP 协议

一个网络有很多的硬件和软件组件构成，如链路，交换机，路由器，主机等。当成千上万的组件组成一个网络，想要把保持网络的持续稳定的运行，是一件很有挑战的事。通过一些网络管理工具和技术可以帮助网络管理员监控，管理，和控制网络。

关键的网络管理组件

- 管理服务器。
- 管理的设备。
- 管理信息。定义了管理信息的格式。
- 网络管理代理。运行在设备上，可以与服务器进行交流。
- 网络管理协议。网络协议允许管理服务器查询和修改设备的状态。网络管理代理可以给管理服务器通知异常时间。常见的网络管理协议 SNMP。

SNMP 协议(The Simple Network Management Protocol) 是一个应用层协议。它在管理服务器和网络管理代理之间传达网络管理控制和信息。

### References

[1] Computer Networking: A Top-Down Approach, by Jim Kurose

[2] [IP 寻址的工作原理](https://blog.51cto.com/suyyzhou/279174)