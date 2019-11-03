---
title: 深入理解计算机网络（五）：链路层
date: 2019-09-07 17:01:27
categories: 
- 计算机基础
- 计算机网络
tags: Network
---

## 介绍链路层

两个相邻的连接的节点之间的通信路径称为链路。任何设备运行一个链路层协议称为一个节点。链路层的主要功能是通过把网络层的数据报封装为链路层的帧（Frame）在链路上传播，从而将网络层数据报从一个节点传输到另一个相邻的节点。链路的信道（Channel）主要有两种类型：点到点通信链路（Point-to-point Link）和广播信道（Broadcast Channel）。常见的点到点通信链路有：以太网链路和广域网链路。

### 链路层提供的服务

- 封装。将网络层的数据报封装成链路层的帧。
- 链路接入。
- 不可靠数据传输。
- 错误侦测和纠正。

### 链路层的硬件组成

网络接口控制器（Network Interface Controller，NIC），也称网卡（Network Card），网络适配器（Network Adapter），局域网适配器，物理网络接口等等。它是一个计算机的硬件组件，它连接了计算机和计算机网络。本篇主要使用网络适配器作为名称，网络适配器的外观如下图所示。

<img class="img-center" src="https://taogenjia.com/img/network-5-link-layer/network-card.jpg" />

在一台主机中应用层、传输层和网络层都是软件组件，而链路层一部分是软件组件，一部分是硬件组件。网络协议栈与主机软件和硬件组件之间的关系如下图所示。

<img class="img-center" src="https://taogenjia.com/img/network-5-link-layer/network-adapter.png" />

链路层软件组件主要和网络层交互。在客户端组装链路层寻址信息，激活网络适配器；在服务端响应网络适配器的 interrupt 信号，传递数据报给网络层。硬件组件功能是错误侦测，以及物理层交互。

## 错误检测与纠正

### 奇偶校验

1\. 简单的奇偶校验 （One-bit Parity）

通过使用一个 parity bit 来侦察错误。将1位 parity bit 放在数据包的后面。

偶数校验，保证 data bits + parity bit 所有 1 的个数等于偶数。如果 data 中所有的1的个数为奇数，则 parity bit 为 1，否则为0。data中所有的1的个数为偶数，则parity bit 为 0。

奇数校验则保证 data bits + parity bit 所有 1 的个数等于奇数。

2\. 二维奇偶检验 （Two-Dimensional Parity，2D parity）

将报文的二进制数据分为 i 行 j 列，每行的末尾和每列的末尾都添加一个 parity bit，一共添加 i+j+1 个 parity bits。它的每行每列都遵循奇偶校验规则。它不仅可以侦测错误，还可以进行错误纠正。

奇偶检验的缺点：

- 简单的奇偶检验和二维奇偶校验都不能侦测所有错误。
- 简单的奇偶检验仅能侦测到一位发生错误。不能侦测一位0和一位1交换错误，两位0或1变为两位1或0错误。
- 二维奇偶校验能更好的侦测错误，但需要更多的检验位。

### 检验和

检验和（Checksum）实现原理是将数据作分为多个16位二进制求和，求和的反码作为检验和携带在报文头部。接收方将数据划分为多个16位与检验和求和，结果所有的位都是 1 则正确，出现 0 表示错误。

### 循环冗余校验

循环冗余校验（Cyclic Redundancy Check （CRC））编码也称多项式编码。要得到 CRC code 需要定义一个生成器多项式。这个多项式的系数作为除数，数据报文作为被除数，得到的余数为 CRC code。如，多项式为 x^7 + x^6 + x^4 + x^3 + x + 1，1x^7 + 1x^6 + 0x^5 +  x^4 + x^3 + 0x^2 + 1x + 1x^0，除数为 11011011。

发送方：

- n 个 0 附加在数据报文后面，n 是位数小于 CRC generator。
- 新的数据单元除以 CRC generator 得到的余数称为 CRC。
- 将数据单元之前添加的 n 个 0 替换成 CRC。
- 数据单元加上 CRC 传输给接收方。

接收方：

- 接收的数据除以 CRC generator，检查余数。
- 如果余数为 0 则数据正确，否则数据错误。 

## 多点连接链路和协议

广播链路可以有多个发送端和接收端同时连接同一个广播信道。解决多个节点同时发送和接收在同一个信道的问题称为多点连接问题（Multiple Access Problem）。多个节点直接通过一个广播信道会产生数据帧的碰撞（Collide），造成数据丢失。多点连接协议就是为了解决多点连接碰撞问题的。常见的多点连接协议类型有：信道划分协议（Channel Partitioning Protocols），随机接入协议（Random Access Protocols），轮流协议（Taking-turns Protocols）

信道划分协议可分为：

- 时分多路复用（Time-Division Multiplexing，TDM）。每个节点轮流使用一小段时间。
- 频分多路复用（Frequency-Division Multiplexing，FDM）。每个节点使用不同的频率。频率的带宽为 R/N bps。
- 码分多址（Code Division Multiple Access，CDMA）。每个节点使用唯一的代码去编码数据。多个的节点可以同时发送和接收编码的数据。

## 局域网

### 链路层寻址和 ARP 协议

链路层通信使用的是 MAC 地址，数据报文的转发依靠 Switch Table 查询目标 MAC 地址对应的输出链路来进行转发。主机和路由器每个网络接口既有一个 IP 地址也有一个 MAC 地址。ARP 协议可以通过广播 ARP 查询请求得到目标 IP 地址对应的 MAC 地址。链路层广播地址为 FF-FF-FF-FF-FF。

#### MAC 地址

MAC 地址是链路层地址，也称物理地址。每个主机或路由器的网络接口都有一个唯一的固定的 MAC 地址。一个 MAC 地址占 6 个字节，使用十六进制数字表示。如，5c-66-AB-90-75-B1。MAC 地址是由 IEEE 组成统一管理的，它保证了世界上每个设备网络接口 MAC 地址是唯一。

为什么有了 IP 地址还需要 MAC 地址呢？因为当设备位置发生改变时，它的 IP 地址也会改变。而 MAC 地址是设置在硬件上的，一般是固定不变的。这样可以唯一的标识一个设备。IP 地址就像是一个人的邮寄地址，而 MAC 地址就行是一个人的身份证号码。 

#### ARP 协议

 ARP（Address Resolution Protocol）协议可以将 IP 地址转换为 MAC 地址。它是怎么做到的呢？每一个主机和路由器都有一个 ARP 表，它记录了一个子网中的 IP 地址对应的 MAC 地址。ARP 表结构如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-5-link-layer/ARP-table.png" />

ARP 只能处理在同一个子网的 IP 地址，因为它使用的是广播的方式。在一个局域网中，一个主机 A 直到另一个主机 B 的 IP 地址，主机 A 发生链路层报文时，需要知道主机 B 的 MAC 地址。其过程如下：

- 主机 A 在它的 ARP 表中寻找主机 B 的记录，如果找到了，使用里面的 MAC 地址即可。
- 如果没找到，则需要通过链路层广播，发送 ARP 查询请求。在这个局域网中的所有主机和路由器都会收到这个请求。
- 每个设备检查 ARP 报文中目标 IP 地址是否与自己的匹配，主机 B 的IP 地址与目标地址匹配，主机 B 单播响应请求，发送的 ARP 响应报文包含自己的 MAC 地址。其它不匹配的主机不做响应。
- 主机 A 接收到响应，知道了主机 B 的 MAC 地址，然后封装 链路层报文与主机 B 通信，并更新自己的 ARP 表。

ARP 时即插即用的，不需要配置。

**发送报文在两个不同的子网**

在同一个局域网两个主机通信是通过 MAC 地址进行数据转发的，如交换机的 switch table 记录了MAC 地址对应的链路接口。

不同的两个局域网需要通过在路由器进行 IP 转发。其过程如下：

- 主机 A 将广播 ARP 请求，想要目标 IP 地址的 MAC 地址。
- 路由器的网络接口发现目标 IP 地址不在这个子网，需要通过路由器 IP 转发，所以路由器响应自己的 MAC 地址给主机 A。
- 主机 A 把路由器当作目标主机。主机 A 把报文发给路由器，路由器通过转发表传给下一跳路由器，经过多个路由器的转发，最终到达目标 IP 地址的主机 B。

### 以太网协议

以太网（Ethernet）是最流行的构建局域网的技术。早先的以太网使用的是广播的形式通信，后来使用交换机存储转发的形式。

以太网的帧结构如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-5-link-layer/Ethernet-frame-structure.png" />

Preamble：唤醒接收端的网络适配器，以及时钟同步。占8字节。

Destination address：表示目标网络适配器的 MAC 地址。占6字节。

Source address：表示源网络适配器 MAC 地址。占6字节。

Type：表示上层协议的类型。占2字节。

Data：IP 报文数据。46 到 1500 字节。

CRC：错误校验。循环冗余检验码。占4字节。



### 链路层交换机

交换机接收链路报文和转发它们到指定的输出链路。交换机对于路由器和主机是透明的，它们不会意识到交换机的存在。交换机没有 IP 地址和 MAC 地址。

交换机的主要功能：

- 过滤。交换机可以判断什么帧可以转发，什么帧需要丢弃。
- 转发。将接收到的帧转发到某个输出链路。

交换机的工作原理：

- 交换机通过查询转换表（Switch Table）来进行转发。交换表格式如下图所示。
- 如果找到了输出链路，则进行转发；没有找到指定的输出链路，则进行广播。

<img class="img-center" src="https://taogenjia.com/img/network-5-link-layer/switch-table.png">

交换机是自学习的，转换表是自动的更新和维护的。初始转换表设置为空。每个请求经过交换机都会更新路由交换表，记录请求中源 MAC 地址对应的输入链路。当一定时间内，转换表中的一个 MAC 地址没有接收任何报文，这个记录就会被删除。

交换机是即插即用不用配置和不同人工干预的。它是全双工的，每个接口可以同时接收和发送数据。

交换机和路由器的对比

|          | Hub  | Router | Switch |
| -------- | ---- | ------ | ------ |
| 连接隔离 | No   | Yes    | Yes    |
| 即插即用 | Yes  | No     | Yes    |
| 最优路由 | No   | Yes    | No     |



### References

[1] Computer Networking: A Top-Down Approach, by Jim Kurose

[2] [Network interface controller - Wikipedia](https://en.wikipedia.org/wiki/Network_interface_controller)

[3] [Cyclic redundancy check - Wikipedia](https://en.wikipedia.org/wiki/Cyclic_redundancy_check)

[4] [Cyclic redundancy check - SlideShare](https://www.slideshare.net/SalehAlrkiyan/cyclic-redundancy-check)