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

Threads are sometimes called lightweight processes. Both processes and threads provide an execution environment, but creating a new thread requires fewer resources than creating a new process.

Threads exist within a process, and every process has at least one thread. Threads share the process's resources, including memory and open files. This makes for efficient, but potentially problematic, communication.

**Difference between processes and threads**

Threads exist within a process, and every process has at least one thread. Both processes and threads are units of execution, and they have an execution environment.

A process is a minimal resource assignment unit, but a thread is a minimal execution unit. 

A process has a self-contained execution environment. No resources are shared between processes. But threads have a private resource from a process, and multiple threads in the same process also can share the resource of their process.

## Thread Objects

Each thread is associated with an instance of the class `Thread`. There are two basic strategies for using `Thread` objects to create a concurrent application.

- To directly control thread creation and management, simply **instantiate `Thread`** each time.
- To abstract thread management from the rest of your application, pass the application's task to an **executor**.

Creating an instance of `Thread` must provide the code that will run in that thread. There are two ways to create `Thread` instance:

- Provide a runnable object.
- Subclass `Thread`.

Using a class of implemented `Runnable` interface to create `Thread` instance is more general, because the `Runnable` object can subclass a class other than `Thread`.

Methods of `Thread` class:

- Constructors
  - `Thread()`, `Thread(String name)`
  - `Thread(Runnable target)`, `Thread(Runnable target, String name)`
  - `Thread(ThreadGroup group, Runnable target, String name)`, `Thread(ThreadGroup group, Runnable target, String name, long stackSize)`
  - `Thread(ThreadGroup group, String name)`
- Static Methods
  - `static in activeCount()`
  - `static Thread currentThread()`
  - `static void dumpStack()`, `static Map<Thread, StackTraceElement[]> getAllStackTraces()`
  - `static int enumerate(Thread[] tarray)`
  - `static Thread.UncaughtExceptionHandler getDefaultUncaughtExceptionHandler()`
  - `static boolean holdsLock(Object obj)`
  - `static boolean interrupted()`
  - `static void yield()`
- Get Thread Information
  - `long getId()`
  - `String getName()`, 
  - `int getPriority()`, 
  - `ClassLoader getConctextClassLoader()`
  - `StackTraceElement[] getStackTrace()`
  - `Thread.State getState()`
  - `ThreadGroup getThreadGroup()`
  - `Thread.UncaughtExceptionHandler getUncaughtExceptionHandler()`
- Check
  - `void checkAccess()`
  - `boolean isAlive()`
  - `boolean isDaemon()`
  - ``boolean isInterrupted()`
- Operating thread
  - `void interrupt()`
  - `void join()`, `void join(long millis)`, `join(long millis, int nanos)`
  - `void run()`
  - `void setName(String name)`, `void setPriority(int newPriority)`, `void setContextClassLoader(ClassLoader c)`, `void setDaemon(boolean on)`
  - `void sleep(long millis)`, `sleep(long millis, int nanos)`
  - `void start()`

[Why are Thread.stop, Thread.suspend and Thread.resume Deprecated?](https://docs.oracle.com/javase/8/docs/technotes/guides/concurrency/threadPrimitiveDeprecation.html)

**Interrupts**

An interrupt is an indication to a thread that it should stop what it is doing and do something else. Thread interruption is a gentle way to stop a thread. It is used to give threads a chance to exit cleanly, as opposed by `Thread.stop()` that force to stop the thread.

**The Interrupt Status Flag**

...

**Difference Between Wait and Sleep**

- `wait` is for concurrent programming but `sleep` not. Sleeping does not release the locks it holds, while waiting releases the lock on `wait()` is called.
- `sleep` just suspends a thread execution for a fixed time, but `wait` suspend a thread execution until `notify` is called. 
- `wait` must happen in a block synchronized on the monitor object whereas `sleep` does not.

```java
Object lock = ...;
synchronized (lock) {
    lock.wait();
} 
```

- You can wait on object itself whereas you call sleep on `Thread`.

**Examples of Thread**

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

[Difference between wait() and sleep()](https://stackoverflow.com/questions/1036754/difference-between-wait-and-sleep)

[What does java.lang.Thread.interrupt() do?](https://stackoverflow.com/questions/3590000/what-does-java-lang-thread-interrupt-do)