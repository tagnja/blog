---
title: 深入理解计算机网络（三）：传输层
date: 2019-08-28 11:29:13
categories: 
- 计算机基础
- 计算机网络
tags: Network
---

## 介绍传输层

### 什么是传输层

应用层提供的服务是让两个终端的进程进行信息交换。而传输层提供是在两个终端的进程之间数据传输服务（Process-to-Process Delivery Service）。传输层的硬件组成部分，和应用层一样依然是两个终端设备，软件部分是传输层协议，如，UDP 和 TCP。

传输层位于应用层和网络层之间。它给应用层提供数据传输服务，同时，它利用了网络层提供的服务。虽然网络层的 IP （Internet Protocol）协议提供的是尽力传递服务（Best-Effort Delivery Service），也就是说网络层提供的是不可靠的数据传输，但是传输层自身可以通过相关的算法，从而实现可靠的数据传输服务。

传输层提供的服务有：

- 数据传输。
- 错误检查。
- 可靠数据传输。数据的正确性和有序性。
- 拥塞控制。

传输层主要有 TCP 和 UDP 两个协议，不同的协议提供不同的服务，适用于不同的应用场景。其中，TCP 协议提供的是可靠传输，它包含了上面所有的服务。而 UDP 协议提供的是不可靠传输，包含部分服务，即数据传输和错误检查。



### 传输层是如何工作的

传输层发送数据的过程：

- 客户端进程组装应用层报文
- 进程根据传输层协议，创建相应的 Socket。
- 进程将应用层消息传给 Socket。
- Socket 将应用层报文（Message）分成多份，加上传输层报文头。封装成传输层报文段（Segment）传递给网络层。

传输层接收数据的过程：

- 网络层将组装好的报文段传给传输层。
- 传输层接收到报文段，根据报文段的头部信息中的目标端口，将报文段传给指定的 Socket。
- Socket 收集到所有的报文段后，将报文段转换为应用层报文。
- 进程读取 Socket 中的应用层报文。

上述过程涉及到的一些重要的概念，接下来进行解释。

**进程**

在操作系统中，一个运行的程序称为一个进程。进程是分配计算机资源的最小单元。网络通信宏观上是两个可计算的终端设备之间的通信，微观上是两个进程之间的通信。

**端口**

端口（Port Number）一个逻辑的实体，它用于在终端设备进行网络通信时标识一个进程。一个进程可以绑定多个端口。一个端口只能属于一个进程。

**网络套接字 （Network Socket）**

Socket 是一个软件组件。它帮助计算机程序连接本地网络或者广域网。Socket 为进程打开网络连接，允许进程通过网络读写数据。Socket 是进程间网络通信的一个终端。Socket 是连接应用层和网络层的大门。

一个 Socket 有三个部分组成：传输层协议，IP 地址，端口。Socket 绑定了一个具体的端口，运输层通过端口知道把报文段传送给哪个进程。



## UDP 协议

UDP 协议的数据传输服务有以下特征：

- 不可靠数据传输
- 无连接
- 最大网速传输
- 更小的报文头部

UDP 协议使用使用起来更简单，不需要三次握手建立连接，没有拥塞控制，简单的报文格式等。如果不需要严格的可靠数据传输，允许部分数据丢失，可以考虑使用 UDP 协议。如，应用层协议 DNS 就是使用 UDP。

UDP 报文段结构

```
Source_port  Dest_port
Length       Checksum
(Application data)
```

UDP 报文段头部共占8字节。由4个字段组成，每个字段2个字节。

Source port：表示客户端进程端口。

Dest port：表示服务端进程端口。

Length：表示报文段的头部和数据的总字节数。

Checksum：用来检查报文段是否出现错误。



## TCP 协议

### 介绍 TCP

TCP 协议有以下特点：

- 可靠的数据传输
- 面向连接

TCP 报文段格式如下图所示：

<img class="img-center" src="https://taogenjia.com/img/network-3-transport-layer-1-tcp-segment-format.png"/>

TCP 报文段头部共占20个字节（Options为空时）。其中

Source port，Dest port，checksum 和 UDP 协议类似。

Sequence number 和 Acknowledgement number 用来实现可靠数据传输服务。

Header length 用来表示报文头部的长度，当 Options 为空时，长度为20。

Flag field 包含6个bit。其中 ACK bit 表示应答客户端是否成功接收，RST，SYN，FIN 用于连接设置。

Receive window 用于流控制。



### TCP 连接管理

客户端进程和服务端进程建立 TCP 连接的过程：三次握手（Three-Way Handshake）

<img class="img-center" src="https://taogenjia.com/img/network-3-transport-layer-2-tcp-setup.png"/>

- 客户端发送一个特殊的 TCP 报文段（SYN segment），请求建立连接。这个特殊的报文段没有应用层数据。报文段头部设置为：SYN bit 设为1；随机设置一个initial sequence number（client_isn）。
- 服务端收到 TCP SYN 报文段。响应给客户端一个 TCP 报文段，这个响应的报文段也没有应用层数据，报文段头部设置为：SYN bit 设为1；acknowledgement number 设为 client_isn + 1；设置一个initial sequence number（server_isn）。
- 客户端收到 SYNACK 报文段。分配连接的缓存和变量。然后发送一个 TCP 报文段给服务端，表示收到服务端的响应。这个报文段同样没有应用层数据，报文段头部设置为：SYN bit 设置为0；acknowledgement number 设置为 server_isn + 1；设置 sequence number 为 client_isn + 1。

> 为什么建立 TCP 连接需要三次握手？
>
> 三次握手为了确定双方都准备好了，确保可以正常通信了。



关闭 TCP 连接的过程：四次挥手

<img class="img-center" src="https://taogenjia.com/img/network-3-transport-layer-3-tcp-close.png"/>

- 客户端请求关闭 TCP 连接。发送一个特殊的报文段，报文段头部设置为： FIN bit 设为1。
- 服务端响应客户端请求。
- 服务端确认可以关闭TCP请求后，发送关闭 TCP 请求。发送一个特殊的报文段，报文头部设置为：FIN bit 设为1.
- 客户端响应服务端请求。

通过四次通信后，客户端和服务端可以关闭 TCP 连接，释放 TCP 连接资源，如缓存和变量。



### 可靠数据传输的实现原理

可靠的数据传输服务要保证服务端接收到的数据有以下特征：

- 没有数据错误
- 没有数据缺少
- 没有数据重复
- 正确的顺序

解决以上问题的方法

- 解决数据错误。通过报文头 checksum 字段的错误校验和错误重发机制，确保没有数据错误。
- 解决数据缺少。通过报文头 acknowledgement number 字段和超时重发机制，确保没有数据缺少。
- 解决数据重复。通过报文头 sequence number 字段，确保没有重复的数据。
- 正确的顺序。通过报文头 sequence number 字段，确保数据的顺序正确。

具体细节没有展开，有兴趣可以阅读参考资料中的书籍。



### 流控制的实现原理

流控制就是控制 TCP 报文发送的速率。它的实现是通过客户端和服务端定义的相关变量来控制的。具体的实现原理如下。

服务端

TCP 连接建立后，服务端分配 TCP 连接缓存，定义 RcvBuffer 变量表示缓存的大小，应用进程读取缓存中的数据。定义两个变量：

LastByteRcvd：网络达到的最后一个字节数。

LastByteRead：应用进程读取的最后一个字节数。

TCP 不允许数据溢出分配的缓存。所以必须保证 LastByteRcvd - LastByteRead <= RcvBuffer

客户端

客户端为了不使服务端的缓存溢出，会根据服务端缓存剩余空间大小，控制发送数据的速度。

客户端定义了一个接受窗口（Receive Window）变量 rwnd。这个变量动态变化的，它等于服务端缓存剩余空间的大小。LastByteSent 表示最后发送的一个字节，它对应服务端的LastByteRcvd。LastByteAcked 表示最后一个应答的字节，它对应服务端的 LastByteRead。变量之间的关系为：

```
rwnd = RcvBuffer - (LastByteSent - LastByteAcked)
```

TCP 连接成功后，服务端会返回它的缓存大小给客户端，客户端初始化自己的接收窗口变量 rwnd = RcvBuffer。然后客户端根据发送和应答的情况动态改变接收窗口变量大小。当 rwnd=0 时，表示服务端的缓存已经满了，客户端则停止发送 TCP 数据。



### 拥塞控制的实现原理

拥塞控制的实现利用了 TCP 的流控制，当拥塞发生时，通过减少客户端的发送 TCP 报文的速率，从而减少网络的拥塞。一般通过是否丢包来判断网络是否拥塞。如何避免拥塞？如何控制客户端的速率更合理？这就是拥塞控制算法需要解决的问题。

造成网络拥塞的原因：

- 路由器排队延迟。数据包到达路由器的速度接近链路容量。
- 不必要的数据包重发。大量的重复的数据包在网络中传播，占用了链路的带宽。
- 丢包。如果一个数据包在第n个路由器被丢弃了，那么这个数据包需要重新在1~n的路由器路径上传递。可以给一个优先权，已经在经过了许多路由器的数据包优先通过路由器。

拥塞控制的方法：

- 端到端拥塞控制（End-to-End Congestion Control）。如果一个 TCP 报文段丢失，表示网络出现拥塞，TCP 自动降低（流控制）接受窗口大小。
- 网络协助拥塞控制（Network-assisted Congestion Control）。网络出现拥塞，路由器直接反馈给客户端。

拥塞控制算法

- 慢热启动（Slow Start）。TCP 开始传输的初始速率很低，然后已指数增长，直到出现丢包后停止增长，速率变为当前速度的一半。
- 拥塞避免（Congestion Avoidance）。当第一次丢包发生后，由慢热启动状态转为拥塞避免状态，传输速率由原来的指数增长，变为线性增长。
- 快速恢复（Fast Recovery）。
  - TCP Tahoe. 如果在拥塞避免的状态下（即线性增长）再次发生丢包。重新回到慢热启动状态，传输速度设为初始速度。
  - TCP Reno. 如果在拥塞避免的状态下（即线性增长）再次发生丢包。传输速度回到拥塞避免状态的初始速度。

通过拥塞控制算法，最终所有的连接会平均分配链路带宽，它们的传输速度会在平均线上下震荡，所有TCP 连接公平的使用网络。



### TCP 协议和 UDP 协议的区别

1. TCP 是面向连接（三次握手），UDP 是无连接的。
2. TCP 提供可靠数据传输服务，UDP 提供不可靠传输。
3. TCP 有拥塞控制（传输速度根据丢包情况调整），UDP 最大速度传输。
4. TCP 报文头 20 字节，UDP 报文头 8 字节



### References

[1] Computer Networking: A Top-Down Approach, by Jim Kurose

[2] [Socket Definition](https://techterms.com/definition/socket)