Title: Tuning HotSpot VM: process and methods

**Content**

- Methodology
- Application Systemic Requirements
- Rank Systemic Requirements
- Choose JVM deployment Model
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

...

## Application Systemic Requirements

...

## References

[1] Java Performance by Charlie Hunt