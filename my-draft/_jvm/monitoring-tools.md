# Java Monitoring Tools

Content

- jps
- jstat
- jstack
- jmap
- jcmd
- jhat

The release of JDK 8 introduced Java Mission Control, Java Flight Recorder, and `jcmd` utility for diagnosing problems with JVM and Java applications. It is suggested to use the latest utility, `jcmd` instead of the previous `jstack`, `jinfo,` and `jmap` utilities for enhanced diagnostics and reduced performance overhead.

## jps

Java Virtual Machine Process Status Tool

```
$ jps
```

Output

```
18027 Java2Demo.JAR
18032 jps
18005 jstat
```



## jstat

This utility uses the built-in instrumentation in Java to provide information about performance and resource consumption of running applications. The tool can be used when diagnosing performance issues, especially those related to heap sizing and garbage collection.

```
$ jstat <option> <PID> <millisecond_intervals> <sample_number>
```

### -gccapacity Option

Memory Pool Generation and Space Capacities

| Column | Description                                 |
| ------ | ------------------------------------------- |
| NGCMN  | Minimum new generation capacity (KB).       |
| NGCMX  | Maximum new generation capacity (KB).       |
| NGC    | Current new generation capacity (KB).       |
| S0C    | Current survivor space 0 capacity (KB).     |
| S1C    | Current survivor space 1 capacity (KB).     |
| EC     | Current eden space capacity (KB).           |
| OGCMN  | Minimum old generation capacity (KB).       |
| OGCMX  | Maximum old generation capacity (KB).       |
| OGC    | Current old generation capacity (KB).       |
| OC     | Current old space capacity (KB).            |
| PGCMN  | Minimum permanent generation capacity (KB). |
| PGCMX  | Maximum Permanent generation capacity (KB). |
| PGC    | Current Permanent generation capacity (KB). |
| PC     | Current Permanent space capacity (KB).      |
| YGC    | Number of Young generation GC Events.       |
| FGC    | Number of Full GC Events.                   |

```
$ jstat -gccapacity <PID> 250 7
```

Output

```
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC 
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
 87040.0 1388544.0 636416.0 7168.0 19968.0 569856.0   175104.0  2777088.0   152576.0   152576.0      0.0 1101824.0  60568.0      0.0 1048576.0   7728.0     18     9
```



### -gcutil Option

Summary of Garbage Collection Statistics

| Column | Description                                                  |
| ------ | ------------------------------------------------------------ |
| S0     | Survivor space 0 utilization as a percentage of the space's current capacity. |
| S1     | Survivor space 1 utilization as a percentage of the space's current capacity. |
| E      | Eden space utilization as a percentage of the space's current capacity. |
| O      | Old space utilization as a percentage of the space's current capacity. |
| P      | Permanent space utilization as a percentage of the space's current capacity. |
| YGC    | Number of young generation GC events.                        |
| YGCT   | Young generation garbage collection time.                    |
| FGC    | Number of full GC events.                                    |
| FGCT   | Full garbage collection time.                                |
| GCT    | Total garbage collection time.                               |

```
$ jstat -gcutil <PID> 250 7
```

Output

```
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT   
  0.00  96.19  48.52  19.95  95.00  92.23     17    0.167     9    0.809    0.976
  0.00  96.19  48.52  19.95  95.00  92.23     17    0.167     9    0.809    0.976
  0.00  96.19  48.52  19.95  95.00  92.23     17    0.167     9    0.809    0.976
  0.00  96.19  48.52  19.95  95.00  92.23     17    0.167     9    0.809    0.976
  0.00  96.19  49.00  19.95  95.00  92.23     17    0.167     9    0.809    0.976
  0.00  96.19  49.00  19.95  95.00  92.23     17    0.167     9    0.809    0.976
  0.00  96.19  49.00  19.95  95.00  92.23     17    0.167     9    0.809    0.976
```



## jstack

This utility can obtain Java and native stack information from a Java process. On Oracle Solaris and Linux operating systems the utility can alos get the information from a core file or a remote debug server.

```
jstack -help
```

```
jstack <pid>  >  <file_path>
```

The `-l` option, which instructs the utility to look for ownable synchronizers in the heap and print information about `java.util.concurrent.locks`. Without this option, the thread dump includes information only on monitors.

```
jstack -l <pid>
```

Mix stack

The `jstack` utility can also be used to print a mixed stack; that is, it can print native stack frames in addition to the Java stack. Native frames are the C/C++ frames associated with VM code and JNI/native code.

```
jstack -m <pid>
```



## jmap

```
$ jmap <option> <pid>
```

options

- -heap: to print java heap summary.
- -histo:  to print histogram of java object heap
- -clstats: to print class loader statistics
- -finalizerinfo: to print information on objects awaiting finalization
- -dump:<dump-options>: to dump java heap in hprof binary format
  - dump-options:
    - live: dump only live objects; if not specified, all objects in the heap are dumped.
    - format=b: binary format
    - file=<file>: dump heap to <file>
    - Example: jmap -dump:live,format=b,file=heap.bin <pid>
- -F: force. Use with -dump:<dump-options> <pid> or -histo to force a heap dump or histogram when <pid> does not respond. The "live" suboption is not supported in this mode.
- -J<flag>: to pass <flag> directly to the runtime system

### Dump

```
$ jmap -dump:live,format=b,file=heap.bin <pid>
```

```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=${HOME}/logs/test
```



A **heap dump** is a snapshot of all the objects in the **Java** Virtual Machine (**JVM**) **heap** at a certain point in time.

**Heap Configuration and Usage**

The -heap option is used to obtain the following Java heap information:

- Information specific to the **garbage collection (GC) algorithm**, including the name of the GC algorithm (for example, parallel GC) and algorithm-specific details (such as **number of threads for parallel GC**).
- **Heap configuration** that might have been specified as command-line options or selected by the VM based on the machine configuration.
- **Heap usage summary**: For each generation (area of the heap), the tool prints the total heap capacity, in-use memory, and available free memory. If a generation is organized as a collection of spaces (for example, the new generation), then a space specific memory size summary is included.

```
$ jmap -heap <PID>
```

Output:

```
Attaching to process ID 1536, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.171-b11

using thread-local object allocation.
Parallel GC with 8 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 4265607168 (4068.0MB)
   NewSize                  = 89128960 (85.0MB)
   MaxNewSize               = 1421869056 (1356.0MB)
   OldSize                  = 179306496 (171.0MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 466616320 (445.0MB)
   used     = 208602816 (198.93914794921875MB)
   free     = 258013504 (246.06085205078125MB)
   44.70542650544242% used
From Space:
   capacity = 26214400 (25.0MB)
   used     = 6952680 (6.630592346191406MB)
   free     = 19261720 (18.369407653808594MB)
   26.522369384765625% used
To Space:
   capacity = 25165824 (24.0MB)
   used     = 0 (0.0MB)
   free     = 25165824 (24.0MB)
   0.0% used
PS Old Generation
   capacity = 156237824 (149.0MB)
   used     = 28791248 (27.457473754882812MB)
   free     = 127446576 (121.54252624511719MB)
   18.427834734820678% used

26001 interned Strings occupying 2663712 bytes.
```



**Heap Histogram**

The jmap command with the -histo option can be used to obtain a class specific histogram of the heap. Depending on the parameter specified, the jmap -histo command can print out the heap histogram for a running process or a core file.

When the command is executed on a running process, the tool prints the number of objects, memory size in bytes, and fully qualified class name for each class. Internal classes in the Java HotSpot VM are enclosed in angle brackets. The histogram is useful in understanding how the heap is used. To get the size of an object, you must divide the total size by the count of that object type.

```
$ jmap -histo <PID>
```

Output

```
 num     #instances         #bytes  class name
----------------------------------------------
   1:       1115968      158673128  [C
   2:       1802142       60277320  [Ljava.lang.Object;
   3:         93117       57795320  [I
   4:        149066       44163016  [B
   5:        689939       27597560  java.util.TreeMap$Entry
   6:        743247       23783904  java.io.ObjectStreamClass$WeakClassKey
   7:        687708       16504992  java.lang.String
   8:        193274        6184768  java.util.TreeMap$KeyIterator
   9:        142029        5635800  [Ljava.lang.String;
  10:        227944        5470656  java.lang.Long
  11:         51096        4267408  [Ljava.util.HashMap$Node;
  12:         85250        4092000  java.util.TreeMap
  ...
```



**Class Loader Statistics**

Use the jmap command with the -clstats option to print class loader statistics for the Java heap.

The jmap command connects to a running process using the process ID and prints detailed **information about classes loaded in the Metaspace**:

- class_loader: The address of the class loader object at the snapshot when the utility was run
- classes: The number of classes loaded
- bytes: The approximate number of bytes consumed by metadata for all classes loaded by this class loader
- parent_loader: The address of the parent class loader (if any)
- alive?: A live or dead indication of whether the loader object will be garbage collected in the future
- type: The class name of this class loader

```
$ jmap -clstats 16624
```

Output

```
Attaching to process ID 1536, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.171-b11
class_loader	classes	bytes	parent_loader	alive?	type

<bootstrap>	2990	5182589	  null  	live	<internal>
0x00000006c2f172f0	1	1471	  null  	dead	sun/reflect/DelegatingClassLoader@0x00000007c000a020
0x00000006c2f1daf0	1	1473	0x00000006c1c15198	dead	sun/reflect/DelegatingClassLoader@0x00000007c000a020
0x00000006c2e776e0	1	1471	  null  	dead	sun/reflect/DelegatingClassLoader@0x00000007c000a020
0x00000006c2304e38	1	1474	0x00000006c1c14260	dead	sun/reflect/DelegatingClassLoader@0x00000007c000a020
0x00000006c2687e60	1	1474	  null  	dead	sun/reflect/DelegatingClassLoader@0x00000007c000a020
0x00000006c2d246d8	1	1084	0x00000006c1c14260	dead	sun/reflect/misc/MethodUtil@0x00000007c07409f0
...
```

## jcmd



## jhat

Once you've dumped the heap into a binary file, you can use `jhat` to analyze the binary heap dump file. `jhat` creates an HTTP/HTML server that can be surfed in a browser, giving an object-by-object view of the heap, frozen in time. While it could be amusing to walk through the heap, object reference by object reference, you're probably better served by doing some kind of automated analysis of the whole mess. Fortunately, `jhat` supports an OQL syntax for doing that analysis.

## References

[Java Platform, Standard Edition Troubleshooting Guide - Monitoring Tools](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr025.html)

[Java performance monitoring - IBM](https://www.ibm.com/developerworks/library/j-5things8/index.html)