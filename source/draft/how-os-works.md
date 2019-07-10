# 操作系统是如何工作的？

outline

- What（定义，结构，组成，功能）
- Why（为什么需要操作系统）
- How（怎么工作，怎么实现了它提供的这些功能）



---



### NOTE

trap, interrupt, Dual-Mode operation, System calls, 

### What

#### 操作系统是什么

操作系统是一个一直运行在计算机中的程序。它管理着计算机的硬件和软件资源，提供一些服务给计算机的程序。一个操作系统像是一个政府组织，它不是为了自己而运作。操作系统提供了一个环境，让程序可以在操作系统中运行，从而为用户提供服务。

### 操作系统提供了哪些功能

- 用户接口。如用户图形界面，终端等。
- 程序的执行。
- I/O 操作。
- 文件系统操作
- 沟通。
- 错误侦探。
- 资源分配。如，为程序分配CPU、分配内存。

### 操作系统的结构

操作系统的结构有很多种，每一种都有它的优缺点。现实中常见的操作系统，如 Windows， Linux， Mac OS 它们采用的结构各不相同。有的采用一种结构，有的采用混合多种结构。下面介绍一些一般的操作系统结构。

简单结构 （Simple Structure）

结构很简单的划分，没有清晰的层次和模块划分，很多功能整合在一起，或者操作系统看起来像是一个整体。这样的结构，会导致系统很难维护。另外，某些功能划分不明确，可能会导致系统出现安全问题和漏洞。

分层结构 （Layered Approach）

// TODO

微核心结构 （Microkernels）

// TODO

模块化结构 （Modules）

// TODO

Windows 结构图

Linux 结构图

Solaris 结构图

虽然每个系统的结构各不相同，但它提供的功能大致是相同的。如进程管理，文件管理，设备管理等功能是必要的。每个系统为了实现它要提供的功能，采用一个合适的结构就好。最重要的是把功能很好的实现，以及方便操作系统能够持续的维护和优化。

### Why

### How

System Calls, interrupt, trap, Dual-Mode operation



### References

[1] Silberschatz, A. Operating system concepts 10th [M]. 2018.

[2] [Operating system - Wikipedia](https://en.wikipedia.org/wiki/Operating_system)