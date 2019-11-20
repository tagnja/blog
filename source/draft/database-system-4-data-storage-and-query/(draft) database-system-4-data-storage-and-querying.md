# Data Storage and Querying (Draft)

Intro

本篇主要介绍数据库的数据存储和查询的物理实现细节。主要分为四个部分：1. 介绍常见的物理存储媒介、文件组织和数据库的存储实现。2. 介绍索引的实现。3. 介绍数据库系统查询处理的过程和实现。4. 介绍数据库系统内部的查询优化。

## Storage and File Structure

数据库系统提供了一个高层级的结构化的数据查看，但是数据最终还是要作为 bits 存储到物理存储设备中。大部分的数据库存储在磁盘中，数据可以传输到内存中进行处理，然后重新保存到磁盘中，也可以拷贝到磁带或其他设备中进行备份。

### Introducing physical storage media

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

一个磁盘的性能好坏的评判标准主要是：capacity，access time，data-transfer rate，and reliability。以下描述了常见评判标准：

- access time：访问时间表示从读或写请求发出到数据可以开始传输之间的时间。
- seek time：寻找时间表示不断重新定位 disk arm 找到正确的 track 的时间。平均的 seek time 为 4 到 10 毫秒。
- rotational latency time：旋转等待时间表示 head 找到了正确的 track 后，等待找到目标 sector 的时间。一般 rotational latency time 为 4 到 11 毫秒。
- data-transfer rate：数据传输速率表示数据可以被取出或存储到磁盘的速率。当今磁盘系统支持最大 data-transfer rate 是 25 到 100 GB 每秒。
- mean time to failure (MTTF)：平均失败时间表示系统连续没有任何错误地运行的总时间的平均值。mean time to failure 一般为 500000 到 1200000 小时，大约 57 到 136 年。

access time 是 seek time 和 rational latency time 的总和，一般为 8 到 20 毫秒。一旦要访问的数据的第一个 sector 在 head 下面时，数据就可以开始传输了。磁盘操作的总时间可以表示为 access time + data transfer time。

Optimization of Disk-Block Access

大部分操作系统中 Disk I/O 请求是由文件系统和虚拟内存管理器生成的。每个请求都指定了要引用的磁盘地址。磁盘地址是由 block number 表示。一个 block 是一个逻辑单元由固定数量的连续的 sector 组成。block 的长度一般是 512 bytes 到 几 KB。数据以 block 为单位在磁盘和内存之间传输。术语 page 常常用来指代 block，小部分环境它们表示不同的事物。

一系列的磁盘 block 请求可以被分类为 sequential access pattern 和 random access pattern，在 sequential access patter 中，连续请求时针对相同 track 或相邻 track 上的连续 block number。当第一个 block 被 seek 到后，后面的 block 不需要 seek 或者 seek 相邻的 track。random access pattern 中，连续请求的 block 是随机的位于磁盘中。每一个请求都需要 seek。

有很多技术可以提升 block 访问的速度，下面是常见的提高磁盘性能的方式：

- Buffering。读取的磁盘的 block 暂时的存储在内存中，方便后面的请求。
- Read-ahead。当一个磁盘的 block 被访问时，与这个 block 相同的 track 下的连续的 blocks 也被读进内存缓存中。顺序访问的情况下，这种预读可减少磁盘 seek 和 rotational latency 的时间。
- Scheduling。安排相同的 cylinder 下的 block 请求一起执行，可以减少 disk-arm 移动的时间。
- File Organization。组织文件的 block，使得要访问的数据在磁盘中距离比较接近。
- Nonvolatile write buffers。当要写入一个 block 到磁盘中，先写入到 nonvolatile write buffers 中并且立即通知曹祖系统写入操作已经完成了。这样可以减少写入的延迟，也不会丢失数据。
- Log disk。另一种减少写入延迟的方法。先把数据写入 log disk 中，写入的 block 是连续写入的，它比 random write 更快，减少了 seek time。之后再写入到数据库中。

### RAID

当数据存储的需求不断增加，单个磁盘容量无法满足增长需求时，我们可以通过组合多个磁盘作为一个磁盘来使用。同时一些应用希望能够增加磁盘的性能和可靠性。这时，我们可以使用 RAID 技术来解决。

大量的提高性能和可靠性的磁盘组织技术统称为 Redundant Arrays of Independent Disks (RAID)。 RAID 它通过 redundancy 提高 reliability，通过 parallelism 提高 performance。

Mirroring 提供了 high reliability，但是它是昂贵的。 Striping 提供了 high data-transfer rates，但是它没有提高可靠性。很多方案致力于提供一个最低花费的 redundancy 结合 disk striping with parity bits。这些方案可以分为不同的 RAID levels。选择不同的 RAID level 我们需要权衡 cost 和 performance。关于 RAID levels 的详细介绍和如何选择可以查阅本文最后给出的参考书籍。

### File Organization

这部分主要介绍数据库的 records 是如何在文件结构中表示的。数据库一般是映射到操作系统维护的许多不同文件中，一个文件逻辑地组织了一系列 records，每个文件是逻辑地划分为固定长度的存储单元，称为 block。这些单元既是存储分配单元也是数据传输单元。大部分数据库的 block size 默认是 4 到 8 KB，很多数据库在数据库实例创建时，允许指定 block size。

每个 block 包含一些 records。每个 record 是完全包含于一个 block 中的，不能被部分包含。这个限制简化和提高了数据的访问。

在关系型数据库中，不同的 relations  的 tuples 通常具有不同的大小。映射数据库到文件的一个方法是使用一些文件，存储固定长度的 records 在任何给定的文件中。另一种方法是对文件进行结构化，以便可以容纳多个不同长度的 records。Fixed-length records 是比 variable-length records 更容易实现的。

Fixed-Length Records

Fixed-length records 方法实现细节：

- 在文件开始位置分配一些 bytes  作为 file header。header 存储关于文件的信息。
- 每个 record 分配固定长度的空间，插入 record 位置不断往后增加。
- 删除一个 record 时，用一个链表记录所有被删除的位置。当链表不为空时，新插入的 record 放置在链表的首元素指向的位置。当链表为空时，新插入的 record 放到最后一个 record 的后面。

Variable-Length Records

Variable-length records 方法的实现主要解决两个问题，一是如何表示可变长度的属性，可以方便的获取属性。二是如何表示可变长度的记录，方便和获取整个记录。

Variable-length records 方法中的属性表示

- 表示 variable-length record 的属性由两个部分组成：初始部分时固定长度属性，如 number，date 等，接着时可变长度属性，如 varchar。
- 可变长度属性，使用一对记录表示在初始位置（offset，length），offset 表示该属性在 record 中开始的位置，length 表示属性的长度。offset 和 length 各占两个字节，一共占4个字节。
- null bitmap 由于指出记录中有一个属性是 null 值。1 表示是 null 值，这个值所占的空间会被忽略，0  表示属性不是 null 值。

variable-length records 的属性表示，如下图所示：

<img src="https://taogenjia.com/img/database-system-4-data-storage-and-querying/database-system-4-data-storage-and-querying-2-variable-length-attributes.png">

Variable-length records 方法中的 record 的表示

- file header 包含了 record 的信息：record 的数量，end of free space，和一个数组包含每个 record 的 location 和 size。
- record 插入时，插入在 end of free space，并且将它的 location 和 size 添加到 header 数组中。
- record 删除时，它前面的元素往后移动，删除的空间会被填充占领。移动的花费是很大的，尤其是当一个 block 存在很多 records 时，需要大量 records 的移动，所以 block 的大小是被限制的，一般为 4 到 8 KB。

Variable-length records 表示 record 的结构图，如下所示：

<img src="https://taogenjia.com/img/database-system-4-data-storage-and-querying/database-system-4-data-storage-and-querying-3-variable-length-records.png">



### Organization of Records in Files

上一节介绍了数据库 records 如何在文件中表示，这一节主要介绍如何在文件中组织 records。

组织 records 可能的方式：

- Heap file organization: records 无顺序放在文件中。
- Sequential file organization: records 根据 search key 的值有序的存储在文件中。
- Hashing file organization: 用 hash function 计算一些属性的值，根据 hash function 的计算结果指定 record 在文件中的位置。

一般一个单独的文件存储一个 relation 的所有 records。然而， multitable clustering file organization 的方式是将多个 relation 的 records 存储在一个文件中，这样做的好处就是：不同 relation 的相关的 records 是存储在相同的 block 中，只需要一次 I/O 操作可以提取所有相关的 records。 

下面将介绍两种常用的 records 在文件中的组织方式。

**Sequential File Organization**

Sequential file 是为了有效处理基于 search key 排序的 records 而设计的。search key 可以是任何一个属性或一组属性，它可以不是 primary key 或者 super key。它通过链表的结构把所有 records 按顺序串联起来，它可以快速的取出以 search-key 为顺序的 records。

sequential file 中的 record 删除：它通过修改链表的指针来删除一个 record。

sequential file 中的 record 插入：找到按顺序插入的位置之前的空位，如果没有空位，就插入在最后一个 record 的后面。

经过一段时间的 insert 和 delete， records 的物理顺序和逻辑顺序是相差很大的，为了保证高效的按顺序处理，它的物理结构是定期 reorganized，尽量保证物理顺序和逻辑顺序是一致的，从而提高存取效率。

**Multitable Clustering File Organization**

multitable clustering file organization 是要给文件组织，它存储两个或多个 relations 中的相关的 records在一个文件中。使用它可以提高某些操作的效率，尤其是 join 操作，但是对于其它操作效率有所减弱。如在 multitable clustering file 中查询单个 relation 的多个 records。可以利用指针将单个 relation 的所有 records 链接起来，提高一点效率。

是否使用 multitable clustering file organization 取决于查询的类型。合理使用可以产生较大的性能提升。

**Data-Dictionary Storage**

relational schema 和 relation metadata 是存储在 data dictionary 或 system catalog 中。它主要存储了以下这些数据：

- relation names。
- 每个 relation 的 attributes names。
- 属性的 domains 和 length。
- view name 和 view definitions。
- integrity constraints。

还有用户的授权和密码相关信息，storage organization，index of relations 等信息。

大部分数据库系统，一般 metadata 作为 relation 存储在数据库中。

### Database Buffer

数据库可以通过内存 buffer 来减少 block 在磁盘和内存之间的传输。Database Buffer 是在内存中存储的磁盘中数据库 block 的 copies。因为内存中没有足够的空间存储整个数据库，所以需要分配一部分空间给数据库作为 buffer。数据库的 buffer manager 是负责缓存空间的管理。

数据库要请求磁盘中的 block 时先请求 buffer manager，如果请求的 block 已经在内存 buffer 中，直接返回。如果不在 buffer 中，buffer manager 请求磁盘 block，并保存在 buffer 中，返回 block 的内存地址给请求者。

buffer manager 的管理策略：

- Buffer replacement strategy。数据库系统使用的 replacement strategy 是 least recently used (LRU)，它也是操作系统虚拟内存管理常用的策略。
- Pinned blocks。block 不允许写入磁盘称为 pinned。当对一个 block 的更新操作正在进行时，这个 block 暂时不允许写入磁盘。
- Forced output of blocks。一个 block 必须写入磁盘中，即使这个 block 不需要被 replacement。这个写入称为 forced output。

## Indexing and Hashing

### Introducing Index

当我们执行查询时，从头到尾一个一个查找是十分低效的。数据库系统使用 index 来提高查询效率，index 是一个文件，它比数据库小，查找数据更方便。数据库系统有很多种 index 技术，常见的 index 类型有：

- Ordered indices。基于有序的数组。可以使用二分法快速查找。
- B+ Tree indices。B+ Tree 可以满足快速的查找，插入和删除。
- Hash indices。通过 hash table 来存储索引。

没有那个 index 技术是最好的，每个技术应用于一些特定的应用场合。评价一个 index 技术的好坏，我们需要综合考虑以下因素：

- Access types。支持的高效访问的类型。如查找特定的属性值，查找特定范围的属性值。
- Access time。找到一个特定数据项的时间。
- Insertion time。插入 record 的时间加维护索引的时间。
- Deletion time。删除 record 的时间加维护索引的时间。
- Space overhead。索引消耗的额外的空间。

### Ordered Indices



### B+ Tree Index File



### B+ Tree Extensions



### Multiple-key Access



### Hashing Index



### Bitmap Indices



### Index Definition in SQL



## Querying Processing

### Introducing Query Processing

### Selection Operation

### Sorting

### Join Operation

### Other Operation

### Evaluation of Expression



## Query Optimization

### Introducing Query Optimization

### Transformation of Relational Expression

### Estimating Statistics of Expression Result

### Choice of Evaluation Plans



Ending



## References

[1] Database System Concept (6th) by Avi Silberschatz, Henry F. Korth, and S. Sudarshan

