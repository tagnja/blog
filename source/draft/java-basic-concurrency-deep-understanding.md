Title: Deep Understanding Java Concurrency

Content

- Introduction
- Processes and Threads
- Thread Objects
- Synchronization
- Liveness
- Guarded Blocks
- Immutable Objects
- High Level Concurrency Objects
- References

## Introduction

Computer user want to do more than one thing at a time. A single application do more than one things at a time that called concurrent software.

The Java platform is designed to support concurrent programming. Since Java SE 5, the Java platform has also included high-level concurrency APIs.

## Processes and Threads

In concurrent programming, there are two basic **units of execution**: process and threads. In the Java programming language, concurrent programming is mostly concerned with threads.

A computer system has many active processes and threads. If in computer systems that only have a single execution core, there are one thread actually executing at any given moment. Processing time for a single core is shared among processes and thread through an OS feature called **time slicing**. 

It's becoming more and more common for computer systems to have multiple processors or processors with multiple execution cores.

**Processes**

A process has a self-contained execution environment. A process generally has a complete, private set of basic run-time resources, for example memory space.

Processes are often seen as synonymous with programs or applications. Most implementations of Java Virtual machine  run as a single process. A Java application can create additional processes using `ProcessBuilder` object.

**Threads**

...

**Difference between processes and threads**

...

## Thread Objects

...

## Synchronization

...

## Liveness

...

## Guarded Blocks

...

## Immutable Objects

...

## High Level Concurrency Objects

...



## Others

[Happens before](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#MemoryVisibility)

[Memory Consistency - happens-before relationship in Java](https://stackoverflow.com/questions/16248898/memory-consistency-happens-before-relationship-in-java)

Thread safe Problem

- Thread safe problems caused by multiple threads share the same object, or reference the same object that injects by its constructors. They lead to false read and unpredicted results.



## References

[1] The Java Tutorial: A Short Course on the Basics