# Data Storage and Querying (Draft)

Intro

本篇主要介绍数据库的数据存储和查询的物理实现细节。主要分为四个部分：1. 介绍常见的物理存储媒介、文件组织和数据库的存储实现。2. 介绍索引的实现。3. 介绍数据库系统查询处理的过程和实现。4. 介绍数据库系统内部的查询优化。

## Storage and File Structure

数据库系统提供了一个高层级的结构化的数据查看，但是数据最终还是要作为 bits 存储到物理存储设备中。大部分的数据库存储在磁盘中，数据可以传输到内存中进行处理，然后重新保存到磁盘中，也可以拷贝到磁带或其他设备中进行备份。

### Introduction to physical storage media

在计算机系统中有很多类型的存储媒介（Storage Media），这些存储媒介可以通过访问速度分类，可以通过每单元容量的价格高低分类，也可以通过稳定性进行分类。常见的存储媒介类型为：

- Cache。缓存是计算机中最快的存储设备。它的容量很小，一般在几 MB。它一般集成在 CPU 中，由计算机硬件进行管理。
- Main Memory。内存一般是用于执行计算机指令。它的容量一般在几GB 到几十 GB 之间。它的速度比较快，访问速度在微秒（Microsecond）级别。它的存储是易失的（Volatile）。对于数据库来说，通常内存的容量不足以存储整个数据库。
- Flash Memory。flash memory 的存储是不易失的（nonvolatile），它广泛使用在小型设备上，如照相机，音乐播放器，手机等。它也用于代替磁盘来存储中等数量的数据，磁盘替代品称为固态硬盘（Solid-State Drives）。它的访问速度在内存和磁盘之间。
- Magnetic-Disk Storage。它是主要的长期在线数据存储媒介。相比于内存，磁盘的访问速度比较慢，一次读取需要几毫秒。磁盘的容量比较大，可以到达几 TB。数据库的数据一般存储在磁盘中。
- Optical Storage。常见的光学存储如 compact disk (CD), digital video disk (DVD) 等。
- Tape Storage。磁带存储主要用于数据备份和归档。磁盘是比较便宜的，但是访问数据比较慢，因为它只能顺序访问，不能随机访问。

选择存储设备，需要综合考虑速度、价格和功能。一般越高的访问速度价格越贵。

存储设备可以分为三个等级：

- Primary Storage: cache，main memory.
- Secondary Storage (or Online Storage): magnetic disk.
- Tertiary Storage (or Offline Storage): magnetic tap, optical disk.

### Magnetic Disk

Physical Characteristics of Disks

磁盘的结构比较简单。一个磁盘一个或多个 platter，platter 是一个圆形的平面，它的两个表面覆盖着磁性材料，信息是存储在 platter 表面的。platter 一般由金属或者玻璃组成。

当磁盘在使用时，drive motor 连续地高速旋转，read-write head 位于 platter 表面上面。磁盘表面逻辑地分为多个 tracks（磁道），每个track 分为多个 sector，sector（扇区）是读写的最小的信息单元。sector 大小一般是 512 bytes。一个磁盘一般有 1 到 5 个 platter，每个 platter 一般有 50000 到 100000 个 track。外圈的 outer tracks 比 里圈的 inner tracks 长度更大。outer tracks 一般有 1000 到 2000 个 sectors，inner tracks 一般有 500 到 1000 个 sectors。read-write head （读写头）以磁性方式将存储信息在一个 sector 中，通过与磁性材料反向磁极的方式。所有 track 的read-write（读写头）安装在 disk arm 上，并且一起移动。所有 platter 的第 i 个 tracks 的组合称为第 i 个 cylinder。磁盘的结构图如下：

<img src="https://taogenjia.com/img/database-system-4-data-storage-and-querying/database-system-4-data-storage-and-querying-1-disk-drive.png">

Performance Measure of Disks

一个磁盘的性能好坏的标准主要是：capacity，access time，data-transfer rate，and reliability。以下描述了常见评判标准：

- access time：访问时间表示从读或写请求发出到数据可以开始传输之间的时间。
- seek time：寻找时间表示不断重新定位 disk arm 找到正确的 track 的时间。平均的 seek time 为 4 到 10 毫秒。
- rotational latency time：旋转等待时间表示 head 找到了正确的 track 后，等待找到目标 sector 的时间。一般 rotational latency time 为 4 到 11 毫秒。
- data-transfer rate：数据传输速率表示数据可以被取出或存储到磁盘的速率。当今磁盘系统支持最大 data-transfer rate 是 25 到 100 GB 每秒。
- mean time to failure (MTTF)：平均失败时间表示系统连续没有任何错误地运行的总时间的平均值。mean time to failure 一般为 500000 到 1200000 小时，大约 57 到 136 年。

access time 是 seek time 和 rational latency time 的总和，一般为 8 到 20 毫秒。一旦要访问的数据的第一个 sector 在 head 下面时，数据就可以开始传输了。磁盘操作的总时间可以表示为 access time + data transfer time。

Optimization of Disk-Block Access



### RAID



### File Organization

### Organization of Records in Files

### Database Buffer



## Indexing and Hashing

Disk is slow。

## Querying Processing

## Query Optimization



Ending



## References



