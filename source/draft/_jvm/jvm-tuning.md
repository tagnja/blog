Title: JVM Tuning

**Content**

- Process of JVM Tuning
- GC Log
- Options
- Tools



## Process of JVM Tuning

一般情况下，JVM调优可通过以下步骤进行：

- 分析GC日志及dump文件，判断是否需要优化，确定瓶颈问题点；
- 确定JVM调优量化目标；
- 确定JVM调优参数（根据历史JVM参数来调整）；
- 依次调优内存、延迟、吞吐量等指标；
- 对比观察调优前后的差异；
- 不断的分析和调整，直到找到合适的JVM参数配置；
- 找到最合适的参数，将这些参数应用到所有服务器，并进行后续跟踪。

以上操作步骤中，某些步骤是需要多次不断迭代完成的。一般是从满足程序的内存使用需求开始的，之后是时间延迟的要求，最后才是吞吐量的要求，要基于这个步骤来不断优化，每一个步骤都是进行下一步的基础，不可逆行之。



## GC Log

### Allocation Failure

"Allocation Failure" is a cause of GC cycle to kick in.

"Allocation Failure" means that no more space left in Eden to allocate object. So, it is normal cause of young GC.

Older JVM were not printing GC cause for minor GC cycles.

"Allocation Failure" is almost only possible cause for minor GC. Another reason for minor GC to kick could be CMS remark phase (if `+XX:+ScavengeBeforeRemark` is enabled).



## Options

### Most Important JVM Parameters

- Heap Memory. Explicit Heap Memory – Xms and Xmx Options
- Garbage Collection. Choosing of right [Garbage Collection](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html) algorithm is critical.
- GC Logging. To strictly monitor the application health, we should always check the JVM's *Garbage Collection* performance. The easiest way to do this is to log the *GC* activity in human readable format.
- Handling out of Memory.
- Misc.

GC Options

- General GC log
  - `-XX:+PrintGCDetails`. Enable GC log and print on console.
  - `-Xloggc:<filename>`. Writing JVM log to a file. e.g `gc_memory_logs.log`
  - `-XX:+PrintGCTimeStamps`. 
  - `-XX:+PrintGCDateStamps`. Readable JVM GC timestamps. e.g  `2010-04-22T18:12:27.796+0200: 22.317: [GC 59030K->52906K(97244K), 0.0019061 secs]`
  - //`-verbose:gc`
- Stopped Time and Concurrent Time
  - `-XX:+PrintGCApplicationConcurrentTime`
  - `-XX:PrintGCApplicationStoppedTime`
- Permanent generation space for metadata information
  - `-XX:PermSize`
  - `-XX:MaxPermSize`

JIT Compiler Options

- `-XX:+PrintCompliation`



## Tools

Offline Analysis of Garbage Collection Data

- GCHisto (Unavailable in 2020)

Monitor

- jconsole
- VisualVM
- jstack 





## References

[1] [JVM性能调优详解](https://juejin.im/post/5dc8d0ea518825592c566a5d)

[1] [Verbose Logging in HotSpot - Documentation](https://docs.oracle.com/javacomponents/jrockit-hotspot/migration-guide/logging.htm#JRHMG121)

[2] [Guide to the Most Important JVM Parameters - Baeldung](https://www.baeldung.com/jvm-parameters)

[3] [Java GC (Allocation Failure) - Stack Overflow](https://stackoverflow.com/questions/28342736/java-gc-allocation-failure)