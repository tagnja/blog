Title: Understanding JRockit VM: Adaptive Memory Management

**Content**

- The concept of automatic memory management
- Fundamental heap management
- Garbage collection algorithms
- Speeding it up and making it scale
- Near-real-time garbage collection
- The Java memory API
- Controlling JRockit memory management

This post is talking about automatic and adaptive memory management in the Java runtime. It mainly includes the following content: concepts of memory management, how a garbage collector works, memory management in the JRockit VM.

## Concepts of Automatic Memory Management

Automatic memory management is defined as any garbage collection technique that automatically gets rid of stale references, making a free operator unnecessary.

There are many implementation of automatic memory management techniques, such as reference counting, heap management strategies, tracing techniques (traversing live object graphs).

**Adaptive memory management**

JVM behavior on runtime feedback is a good idea. JRockit was the first JVM to recognize that adaptive optimization based on runtime feedback could be applied to all subsystems in the runtime and not just to code generation. One of these subsystems is memory management.

Adaptive memory management is based heavily on runtime feedback. It is a special case of automatic memory management.

Adaptive memory management must correctly utilize runtime feedback for optimal performance. This means changing GC strategies, automatic heap resizing, getting rid of memory fragmentation at the right intervals, or mainly recognizing when it is most appropriate to "stop the world". **Stopping the world** means halting the executing Java program, which is a necessary evil for parts of a garbage collection cycle.

**Advantages of automatic memory management**

- It contributes to the speed of the software development cycle. Because using automatic memory management, memory allocation bugs can't occur and buffer overruns can't occur.
- An adaptive memory manager may pick the appropriate garbage collection strategy for an application based on its current behavior, appropriately changing the number of garbage collecting threads or fine tuning other aspects of garbage collection strategies whenever needed.

**Disadvantages of automatic memory management**

- It can slow down execution for certain applications to such an extent that it becomes impractical.
- There may still be memory leaks in a garbage collected environment.

## Fundamental Heap Management







## References

[1] Oracle JRockit: The Definitive Guide by Marcus Hirt, Marcus Lagergren

