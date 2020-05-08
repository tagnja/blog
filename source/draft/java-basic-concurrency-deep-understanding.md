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

An interrupt is an indication to a thread that it should stop what it is doing and do something else. `Thread.interrupt()` sets the interrupted status/flag of the target thread. The code running in that target thread MAY poll the interrupted status and handle it appropriately. Some methods that block such as `Object.wait()` may consume the interrupted status immediately and throw an appropriate exception (usually `InterruptedException`). Thread interruption is a gentle way to stop a thread. It is used to give threads a chance to exit cleanly, as opposed by deprecated `Thread.stop()` that force to stop the thread.

Interruption in Java is not preemptive. Threads have to cooperate in order to process the interrupt properly. If the target thread does not poll the interrupted status the interrupt is effectively ignored. Polling occurs via the `Thread.interrupted()` method which returns the current thread's interrupted status and clears that interrupt flag. Usually the thread might then do something such as throw `InterruptedException`. If a thread goes a long time without invoking a method that throws `InterruptedException` Then it must periodically invoke `Thread.interrupted()`, which returns if an interrupt has been received. For example:

```java
while (...){
    doSometing();
    if (Thread.interrupted()){
        // We've been interrupted
        System.out.println("Thread interrupted!");
        return; // or break loop
    }
}
```

```java
if (Thread.interrupted()){
	throw new InterrupteException();
}
```

Some API methods have built in interrupt handling

- `Object.wait()`
- `Thread.sleep()`, `Thread.join()`
- Most `java.util.concurrent` structures.
- Java NIO (it does not use `InterruptedException`, instead using `ClosedByInterruptException`).

**Joins**

The `join` method allows one thread to wait for the completion of another. Example of current thread wait for thread t to be complete:

```java
t.join()
```

`join` is similar to `sleep` doesn't release the locks it holds, it just suspends the current thread.

**Difference Between Wait and Sleep**

- `wait` is for concurrent programming but `sleep` not. Sleeping does not release the locks it holds, while waiting releases the lock on `wait()` is called.
- `sleep` just suspends a thread execution for a fixed time, but `wait` suspend a thread execution until `notify` is called. 
- `wait` must happen in a block synchronized on the monitor object (otherwise occurs `IllegalMonitorStateException` ) whereas `sleep` does not.

```java
Object lock = ...;
synchronized (lock) {
    lock.wait();
} 
```

- You can wait on object itself whereas you call sleep on `Thread`.

## Synchronization

Threads communicate primarily by sharing access to fields and the objects reference fields refer to. This form of communication is extremely efficient, but makes two kinds of errors possible: thread interference and memory consistency errors. The tool needed to prevent these errors is **synchronization**.

However, synchronization can introduce threads deadlock and thread contention (starvation and livelock). 

- Thread Interference: Errors are introduced when multiple threads access shared data.
- Memory Consistency Errors: Errors that result form inconsistent views of shared memory.
- Synchronized Methods: It can effectively prevent thread interference and memory consistency errors.
- Implicit Locks: synchronization is based on implicit locks.
- Atomic Access: operations that can't be interfered with by other threads.

**Thread Interference**

```java
class Counter {
    private int c = 0;
    public void increment(){
        c++;
    }
    public void decrement(){
        c--;
    }
    public int value(){
        return c;
    }
}
```

The increment() and decrement() are not atomic operations. Each method has three steps operations: retrieve variable, update value, store result in variable. When multiple threads invoke these methods, they interfere with each other, because the three steps of each thread are interleaving executed. So the result of each thread is unpredictable.

**Memory Consistency Errors**

Memory consistency errors occur when different threads have inconsistent views of what should be the same data. When other thread update value of a variable, your current thread still read old value of the variable.

The key to avoiding memory consistency errors is understanding the **happens-before** relationship. This relationship is simply a guarantee that memory writes by one specific statement are visible to another specific statement.

There are several actions that create happens-before relationships:

- Single thread rule: Each action in a single thread happens-before every action in that thread that comes later in the program order.
- Monitor lock rule (synchronization): An unlock on a monitor lock (exiting synchronized method/block) happens-before every subsequent acquiring on the same monitor lock.
- Volatile variable rule: A write to a volatile field happens-before every subsequent read of that same field.
- Thread start rule: A call to `Thread.start()` on a thread happens-before every action in the started thread.
- Thread join rule: All actions in a thread happen-before any other thread successfully returns from a join on that thread.
- Transitivity: If A happens-before B, and B happens-before C, then A happens-before C.

**Synchronized Methods**

The Java programming language provides two basic synchronization idioms: synchronized methods and synchronized statements. `synchronized` make non-atomic operations become to atomic operations and establish happens-before relationships between threads that access the same variables.



Intrinsic Locks and Synchronization

...

Atomic Access

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

[Java - Understanding Happens-before relationship](https://www.logicbig.com/tutorials/core-java-tutorial/java-multi-threading/happens-before.html)