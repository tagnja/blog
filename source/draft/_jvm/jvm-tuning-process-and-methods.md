Title: Tuning HotSpot VM: process and methods

**Content**

- Methodology
- Application Systemic Requirements
- Rank Systemic Requirements
- Choose JVM Deployment Model
- Choose JVM Runtime
- GC Tuning Fundamentals
- Determine Memory Footprint
- Tune Latency/Responsiveness
- Tune Application Throughput
- Conclusion
- References

Many applications perform well with the default settings of a JVM, but some applications require additional JVM tuning to meet their performance requirement. A well-tuned JVM configurations used for one application may not be well suited for another application. As a result, understanding how to tune a JVM is a necessity.

## Process of JVM Tuning

The following figure shows the process of JVM tuning.

<img class="img-center" src="jvm-tuning-process.png" />

The first thing of JVM tuning is prioritize application systemic requirements. In contrast to functional requirements, which indicate functionally what an application computes or produces for output, systemic requirements indicate a particular aspect of an application's such as its throughput, response time, the amount of memory it consumes, startup time, availability, manageability, and so on.

Performance tuning a JVM involves many trade-offs. When you emphasize one systemic requirement, you usually sacrifice something in another. For example, minimizing memory footprint usually comes at the expense of throughput and/or latency. As you improve manageability, you sacrifice some level of availability of the application since running fewer JVMs puts a larger portion of an application at risk should there be an unexpected failure. Therefore, when emphasizing systemic requirements, it is crucial to the tuning process to understand which are most important to the application.

Once you know which systemic requirements are most important, the following steps of tuning process are: 

- Choose a JVM deployment mode.
- Choose a JVM runtime environment.
- Tuning the garbage collector to meet your application's memory footprint, pause time/latency, and throughput requirements.

For some applications and their systemic requirements, it may take several iterations of this process until the application's stakeholders are satisfied with the application's performance.

**Application Execution Assumptions**

- An initialization phase where the application initializes important data structures and other necessary artifacts to begin its intended use.
- A steady state phase where the application spends most of its time and where the application's core functionality is executed.
- An optional summary phase where an artifact such as a report may be generated, such as that produced by executing a benchmark program just prior to the application ending its execution.

The steady state phase where an application spends most of its time is the phase of most interest.

**Testing Infrastructure Requirements**

The performance testing environment should be close to the production environment. The better the testing environment replicates the production environment running with a realistic production load, the more accurate and better informed tuning decision will be.

## Application Systemic Requirements

There are several application systemic requirements,  such as its throughput, response time, the amount of memory it consumes, its availability, its manageability, and so on. 

**Availability**

Availability is a measure of the application being in an operational and usable state. An availability requirement expresses to what extent an application, or portions of an application, are available for use when some component breaks or experiences a failure.

In the context of a Java application, higher availability can be accomplished by running portions of an application across multiple JVMs or by multiple instances of the application in multiple JVMs. One of the trade-offs when emphasizing availability is increased manageability costs.

**Manageability**

Manageability is a measure of the operational costs associated with running and monitoring the application along with how easy it is configure the application. A manageability requirement expresses the ease with which the system can be managed. Configuration tends to be easier with fewer JVMs, but the application's availability may be sacrificed.

**Throughput**

Throughput is a measure of the amount of work that can be performed per unit time. A throughput requirement ignores latency or responsiveness. Usually, increased throughput comes at the expense of either an crease in latency and/or an increase in memory footprint.

**Latency and Responsiveness**

Latency, or responsiveness, is a measure of the elapsed time between when an application receives a stimulus to do some work and that work is completed. A latency or responsiveness requirement ignores throughput. Usually, increased responsiveness or lower latency, comes at the expense of lower throughput and/or an increase in memory footprint.

**Memory Footprint**

Memory footprint is a measure of the amount of memory required to run an application at a some level of throughput, some level of latency, and/or some level of availability and manageability. Memory footprint is usually expressed as either the amount of Java heap required to run the application and/or the total amount of memory required to run the application.

**Startup Time**

Startup time is a measure of the amount of time it takes for an application to initialize. The time it takes to initialize a Java application is dependent on many factors including but not limited to the number of classes loaded, the number of objects that require initialization, how those objects are initialized, and the choice of a HotSpot VM runtime, that is, client or server.

## Rank Systemic Requirements

The first step in the tuning process is prioritizing the application's systemic requirements. Doing so involves getting the major application stakeholders together and agreeing upon the prioritization. 

Ranking the systemic requirements in order of importance to the application stakeholders is critical to the tuning process. The most important systemic requirements drive some of the initial decisions.

## Choose JVM Deployment Model

There is not necessarily a best JVM deployment model. The most appropriate choice depends on which systemic requirements (manageability, availability, etc.) are most important.

Generally, with JVM deployment models has been the fewer the JVMs the better. With fewer JVMs, there are fewer JVMs to monitor and manage along with less total memory footprint.

## Choose JVM Runtime

Choosing a JVM runtime for a Java application is about making a choice between a runtime environment that tends to be better suited for one or another of client and server applications.

There are several runtime environments to consider: client or server runtime, 32-bit or 64-bit JVM, and garbage collectors.

**Client or Server Runtime** 

There are three types of JVM runtime to choose from when using the HotSpot VM: client, server, or tiered.

- The client runtime is specialized for rapid startup, small memory footprint, and a JIT compiler with rapid code generation.
- The server runtime offers more sophisticated code generation optimizations, which are more desirable in server applications. Many of the optimizations found in the server runtime's JIT compiler require additional time to gather more information about the behavior of the program and to produce better performing generated code.
- The tiered runtime which combines the best of the client and server runtimes, that is, rapid startup time and high performing generated code.

If you do not know which runtime to initially choose, start with the server runtime. If startup time or memory footprint requirements cannot be met and you are using Java 6 Update 25 or later, try the tiered server runtime. If you are not running Java 6 Update 25 or later, or the tiered server runtime is unable to meet your startup time or memory footprint requirement, switch to the client runtime.

**32-Bit or b4-Bit JVM**

There is a choice between 32-bit and 64-bit JVMs. 

The following table provides some guidelines for making an initial decision on whether to start with a 32-bit or 64-bit JVM. Note that client runtimes are not available in 64-bit HotSpot VMs.

| Operating System | Java Heap Size                          | 32-Bit or 64-Bit JVM                                         |
| ---------------- | --------------------------------------- | ------------------------------------------------------------ |
| Windows          | Less than 1300 megabytes                | 32-bit                                                       |
| Windows          | Between 1500 megabytes and 32 gigabytes | 64-bit with -d64 -XX:+UseCompressedOops command line options |
| Windows          | More than 32 gigabytes                  | 64-bit with -d64 command line option                         |
| Linux            | Less than 2 gigabytes                   | 32-bit                                                       |
| Linux            | Between 2 and 32 gigabytes              | 64-bit with -d64 -XX:+UseCompressedOops command line options |
| Linux            | More than 32 gigabytes                  | 64-bit with -d64 command line option                         |
| Oracle Solaris   | Less than 3 gigabytes                   | 32-bit                                                       |
| Oracle Solaris   | Between 3 and 32 gigabytes              | 64-bit with -d64 -XX:+UseCompressedOops command line options |
| Oracle Solaris   | More than 32 gigabytes                  | 64-bit with -d64 command line option                         |

**Garbage Collectors**

There are several garbage collectors are available in the HotSpot VM: serial, throughput, mostly concurrent, and garbage first.

Since it is possible for applications to meet their pause time requirements with the throughput garbage collector, start with the throughput garbage collector and migrate to the concurrent garbage collector if necessary. If migration to the concurrent garbage collector is required, it will happen later in the tuning process as part of determine and tune application latency step.

The throughput garbage collector is specified by the HotSpot VM command line option `-XX:+UseParallelOldGC` or `-XX:+UseParallelGC`. The difference between the two is that the `-XX:+UseParallelOldGC` is both a multithreaded young generation garbage collector and a multithreaded old generation garbage collector. `-XX:+UseParallelGC` enables only a multithreaded young generation garbage collector.

## GC Tuning Fundamentals

**The Performance Attributes**

**The Principles**

**Command Line Options and GC Logging**



## Determine Memory Footprint

...

- Tune Latency/Responsiveness
- Tune Application Throughput
- Conclusion

## References

[1] Java Performance by Charlie Hunt