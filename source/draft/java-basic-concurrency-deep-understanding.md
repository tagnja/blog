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
  - Lock Objects
  - Executors
  - Concurrent Collections
  - Atomic Variables
  - Concurrent Random Numbers
- Conclusion
- References

## Introduction

Computer users want to do more than one thing at a time. A single application does more than one things at a time called concurrent software.

The Java platform is designed to support concurrent programming. Since Java SE 5, the Java platform has also included high-level concurrency APIs.

## Processes and Threads

In concurrent programming, there are two basic **units of execution**: process and threads. In the Java programming language, concurrent programming is mostly concerned with threads.

A computer system has many active processes and threads. If in computer systems that only have a single execution core, there is one thread actually executing at any given moment. Processing time for a single core is shared among processes and threads through an OS feature called **time slicing**. 

It's becoming more and more common for computer systems to have multiple processors or processors with multiple execution cores.

**Processes**

A process has a self-contained execution environment. A process generally has a complete, private set of basic run-time resources, for example, memory space.

Processes are often seen as synonymous with programs or applications. Most implementations of Java Virtual machines run as a single process. A Java application can create additional processes using `ProcessBuilder` object.

**Threads**

Threads are sometimes called lightweight processes. Both processes and threads provide an execution environment, but creating a new thread requires fewer resources than creating a new process.

Threads exist within a process, and every process has at least one thread. Threads share the process's resources, including memory and open files. This makes for efficient, but potentially problematic, communication.

**Difference between processes and threads**

Threads exist within a process, and every process has at least one thread. Both processes and threads are units of execution, and they have an execution environment.

A process is a minimal resource assignment unit, but a thread is a minimal execution unit. 

A process has a self-contained execution environment. No resources are shared between processes. But threads have a private resource from a process, and multiple threads in the same process also can share the resource of their process.

**The Advantages and Disadvantages of Threads**

Advantages

- Improve the performance of applications.
- Asynchronous workflows.
- Exploiting multiple processors.
- More responsive user interfaces.

Disadvantages

- Safety.
- Liveness. 
- Performance. Thread introduces additional performance costs, for example, context switches, and synchronization costs.

Properly using multi-thread is more beneficial than disadvantages.

## Thread Objects

Each thread is associated with an instance of the class `Thread`. There are two basic strategies for using `Thread` objects to create a concurrent application.

- To directly control thread creation and management, simply **instantiate `Thread`** each time.
- To abstract thread management from the rest of your application, pass the application's task to an **executor**.

Creating an instance of `Thread` must provide the code that will run in that thread. There are two ways to create a `Thread` instance:

- Provide a runnable object.
- Subclass `Thread`.

Using a class of implemented `Runnable` interface to create an instance of `Thread` is more general because of the `Runnable` object can subclass a class other than `Thread`.

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

Interruption in Java is not preemptive. Threads have to cooperate in order to process the interrupt properly. If the target thread does not poll the interrupted status the interrupt is effectively ignored. Polling occurs via the `Thread.interrupted()` method which returns the current thread's interrupted status and clears that interrupt flag. Usually, the thread might then do something such as throw `InterruptedException`. If a thread goes a long time without invoking a method that throws `InterruptedException` Then it must periodically invoke `Thread.interrupted()`, which returns if an interrupt has been received. For example:

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

Some API methods have built-in interrupt handling

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

- `wait` is for concurrent programming but `sleep` not. Sleeping does not release the locks it holds while `wait` releases the lock on `wait()` is called.
- `sleep` just suspends a thread execution for a fixed time, but `wait` suspends a thread execution until `notify` is called. 
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

The key to avoiding memory consistency errors is understanding the **happens-before** relationship. This relationship is simply a guarantee that memory writes by one specific statement is visible to another specific statement.

There are several actions that create happens-before relationships:

- Single thread rule: Each action in a single thread happens-before every action in that thread that comes later in the program order.
- Monitor lock rule (synchronization): An unlock on a monitor lock (exiting synchronized method/block) happens-before every subsequent acquiring on the same monitor lock.
- Volatile variable rule: A write to a volatile field happens-before every subsequent read of that same field.
- Thread start rule: A call to `Thread.start()` on a thread happens-before every action in the started thread.
- Thread join rule: All actions in a thread happen-before any other thread successfully returns from a join on that thread.
- Transitivity: If A happens-before B, and B happens-before C, then A happens-before C.

**Synchronized Methods**

The Java programming language provides two basic synchronization idioms: **synchronized methods** and **synchronized statements**. `synchronized` make non-atomic operations become atomic operations and establish happens-before relationships between threads that access the same variables.

```java
class SynchronizedCounter {
    private int c = 0;
    public synchronized void increment(){
        c++;
    }
    public synchronized void decrement(){
        c--;
    }
    public synchronized int value(){
        return c;
    }
}
```

Synchronized methods enable a simple strategy for preventing thread interference and memory errors. First, it is not possible for two invocations of synchronized methods on the same object to interleave. Second, when a synchronized method exists, it automatically establishes a happens-before relationship with any subsequent invocation of a synchronized method for the same object.

**Intrinsic Locks and Synchronization**

Synchronization is built around an internal entity known as the intrinsic lock or monitor lock. (The API specification often refers to this entity simply as a “monitor.”) Intrinsic locks play a role in both aspects of synchronization: enforcing exclusive access to an object's state and establishing happens-before relationships. 

Every object has an intrinsic lock associated with it.

Locks in Synchronized Methods

When a thread invokes a synchronized method, it automatically acquires the intrinsic lock for that method's object and release it when the method returns. The lock release occurs even if the return was caused by an uncaught exception.

A static synchronized method is associated with a class. A thread acquires the intrinsic lock for the Class object associated with the class.

Synchronized Statements

```java
public void add(String name){
    synchronized(this){
        lastName = name;
        nameCount++;
    }
    nameList.add(name);
}
```

Invoking other objects' methods from synchronized code can create liveness problems.

Synchronized statements are also useful for improving concurrency with fine-grained synchronization.

Reentrant Synchronization

Allowing a thread to acquire the same lock more than once enable reentrant synchronization.

**Atomic Access**

Common Atomic Actions

- Reads and writes are atomic for reference variables and for most of primitive variables (except `long` and `double`).
- Reads and writes are atomic for all variable declared `volatile` (including `long` and `double` variables)

Atomic actions cannot be interleaved, so they can be used without fear of thread interference. However, this does not eliminate all need to synchronize atomic actions, because memory consistency errors are still possible. Using `volatile` variables reduce the risk of memory consistency errors, because any write to a `volatile` variable establishes a happens-before relationship with subsequent reads of the same variable.

Using simple atomic variable access is more efficient than accessing these variables through synchronized code, but requires more care by the programmer to avoid memory consistency errors. Some of the classes in the `java.util.concurrent` package provide atomic methods that do not rely on synchronization.

## Liveness

The most common kind of liveness problem is the deadlock, others are starvation and livelock.

**Deadlock**

Deadlock describes a situation where two or more threads are blocked forever, waiting for each other.

```java
public class DeadlockExample{
    private static Object lock1 = new Object();
    private static Object lock2 = new Object();

    public static void main(String[] args) {
        new Thread(()->{
            synchronized (lock1){
                try {
                    // ensure both two threads acquired their first lock, 
                    // and waiting for acquired the second lock.
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (lock2){}
            }
        }).start();
        new Thread(()->{
            synchronized (lock2){
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (lock1){}
            }
        }).start();
    }
}
```

**Starvation and Livelock**

Starvation and livelock are much less common a problem than deadlock, but are still problems that every designer of concurrent software is likely to encounter.

Starvation describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress.

Livelock is a situation threads simply too busy responding to each other and unable to make further progress.

## Guarded Blocks

Threads often have to coordinate their actions. The most common coordination idiom is the guarded block. Such a block begins by polling a condition that must be true before the block can proceed.

Guard by simply loop

```java
public void guardedJoy(){
	while(!joy) {}
	System.out.println("Joy has been achived!");
}
```

Guard by invokes `Object.wait`. A more efficient guard.

```java
public synchronized void guardedJoy(){
    while(!joy){
        try{
            wait();
        } catch (InterruptedException e){}
    }
    System.out.println("Joy efficiently has been achived!");
}
```

```java
public synchronized notifyJoy() {
    joy = true;
    notifyAll();
}
```

Using guarded blocks can create a Producer-Consumer application.

## Immutable Objects

An object is considered immutable if its state connot change after it is constructed.

Immutable objects are particularly useful in concurrent applications. Since they cannot change state, they cannot be corrupted by thread interference or observed in an inconsistent state.

Immutable objects using strategies

- Don't provide "setter" methods. Methods that modify fields or objects referred to by fields.
- Make all fields `final` and `private`.
- Don't allow subclass to override methods. The simplest way to do this is to declare the class as `final`. A more sophisticated approach is to make the constructor private and construct instances in factory methods in this class.
- If the instance fields include references to mutable objects, don't allow those objects to be change. Don't provide methods that modify the mutable objects. Don't share references to the mutable objects.

## High Level Concurrency Objects

These low-level APIs are adequate for very basic tasks, but higher-level building blocks are needed for more advanced tasks. This is especially useful for massively concurrent applications in multiprocessor and multi-core systems.

High Level Concurrency Features:

- Lock object support locking idioms that simplify many concurrent applications.
- Executors define a high-level API for launching and managing threads. Executor implementations provided by `java.util.concurrent` provide thread pool management suitable for large-scale applications.
- Concurrent collections make it easier to manage large collections of data, and can greatly reduce the need for synchronization.
- Atomic variables have features that minimize synchronization and help avoid memory consistency errors.
- `ThreadLocalRandom` provides efficient generation of pseudorandom numbers from multiple threads.

**Lock Objects**

Synchronized code relies on a simple kind of reentrant lock. This kind of lock is easy to use, but many limitations. More sophisticated locking idioms are supported by the `java.util.concurrent.locks` package.

Lock objects work very much like the implicit locks used by synchronized code. The biggest advantage of `Lock` objects over implicit locks is their ability to back out of an attempt to acquire a lock. The `tryLock` method backs out if the lock is not available immediately or before a timeout expires. 

We can use Lock objects to solve the deadlock problem. First we use `Lock.tryLock()` method to acquire all locks we needed, if fail to acquire, then unlock all acquired locks, else we can acquire all locks and without deadlock problem.

**Executors**

In large-scale applications, it makes sense to separate thread management and creation from the rest of the application. Objects that encapsulate these functions are known as `executors`.

- Executor Interfaces: defines the three executor object types.
- Thread Pools: are the most common kind of executor implementation.
- Fork/Join: is a framework for taking advantage of multiple processors.
- Executors: is a utility class that has factory and utility methods for Executor, ExecutorService, ScheduledExecutorService, ThreadFactory, and Callable classes.

Hierarchy of Executor

```
(I)Executor
|----(I)ExecutorService
|--------(I)ScheduledExecutorService
|--------(A)AbstractExecutorService
|------------ForkJoinPool
|------------ThreadPoolExecutor
|----------------ScheduledThreadPoolExecutor
Executors
```

Executor Interfaces

The `java.util.concurrent` package defines three executor interfaces: `Executor`, `ExecutorService`, `ScheduledExecutorService`. Typically, variables that refer to executor objects are declared as one of these three interface types, not with an executor class type.

The `Executor` interface provides a single method `execute()` designed to be a replacement for a common thread-creation idiom.

```java
new Thread(r).start();
```

```java
executor.execute(r);
```

The `ExecutorService` interface provides methods `execute` and `submit`. Like `execute`, the `submit` method accepts `Runnable` objects, but also accepts `Callable` object, which allow the task to return a value. The `submit` method returns a `Future` object.

The `ScheduledExecutorService` interface supplements the methods of its parent `ExecutorService` with `schedule`, which executes a `Runnable` or `Callable` task after a specified delay. In addition, the interface defines `scheduleAtFixedRate` and `scheduleWithFixedDelay`, which executes specified tasks repeatedly, at defined intervals.

Thread Pools

Most of executor implementations use thread pools. One common type of thread pool is the fixed thread pool.

One common type of thread pool is the fixed thread pool. A simple way to create an executor that uses a fixed thread pool is to invoke the `newFixedThreadPool` factory method of `java.util.concurrent.Executors`. This class also provides the following factory methods:

- `newCachedThreadPool`
- `new SingleThreadExecutor`
- several factory methods returns `ScheduledExecuteorService` executors.

If none of the executors provided by the factory methods of `Executors` meet your needs, constructing instance of `ThreadPoolExecutor` or `ScheduledThreadPoolExecutor` will give you additional options.

To construct a `ThreadPoolExecutor` object, you least specify five arguments: 

- `ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue)`

Fork/Join

The fork/join framework is an implementation of `ExecutorService` interface that helps you take advantage of multiple processors. It is designed for work that can be broken into smaller pieces recursively.

The fork/join framework is distinct because it uses a work-stealing algorithm. Worker threads that run out of things to do can steal tasks from other threads that are still busy.

The center of the fork/join framework is the `ForkJoinPool` class, an extension of the `AbstractExecutorService` class. `ForkJoinPool` implements the core work-stealing algorithm and can execute `ForkJoinTask` processes.

You submit tasks to a `ForkJoinPool` similarly to how you submit tasks to an `ExecutorService`. You can submit two types of tasks. One is `RecursiveAction` that does not return any result. Another is `RecursiveTask` can return a result. They are a subclass of `ForkJoinTask`, and all of the tasks classes are abstract.

There are some generally useful features in JavaSE which are already implemented using the fork/join framework.

- `Arrays` class methods: `parallelSort()`
- `java.util.stream` package

fork/join framework pseudocode:

```
if (my portion of the work is small enough)
	do the work directly
else
	split my work into two pieces
```

Example of `ForkJoinPool`

```java
public class Main {
    public static void main(String[] args) {
        ForkJoinPool forkJoinPool = new ForkJoinPool(5);
        forkJoinPool.invoke(new MyRecursiveAction(100));
    }
}
```

```java
public class MyRecursiveAction extends RecursiveAction {
    private long workload = 0;
    private static final long THRESHOLD = 16;
    
    public MyRecursiveAction(long workload) {
        this.workload = workload;
    }

    @Override
    protected void compute() {
        if (this.workload <= THRESHOLD) {
            System.out.println("Doing workload myself : " + this.workload);
        } else {
            System.out.println("Splitting workload : " + this.workload);
            List<MyRecursiveAction> subtasks = new ArrayList<>();
            subtasks.addAll(createSubtasks());
            for (RecursiveAction subtask : subtasks) {
                subtask.fork();
            }
        }
    }

    private List<MyRecursiveAction> createSubtasks() {
        List<MyRecursiveAction> subtasks = new ArrayList<>();
        MyRecursiveAction subtask1 = new MyRecursiveAction(this.workload / 2);
        MyRecursiveAction subtask2 = new MyRecursiveAction(this.workload / 2);
        subtasks.add(subtask1);
        subtasks.add(subtask2);
        return subtasks;
    }
}
```



**Concurrent Collections**

Concurrent collections are high concurrency and thread-safe collection implementation. For example, `CopyOnWriteArrayList`, `ConcurrentHashMap`.

**Atomic Variables**

The `java.util.concurrent.atomic` package defines classes that support atomic operations on single variable. All classes have `get` and `set` methods that work like reads and writes on volatile variables. A `set` has a happens-before relationship with any subsequent `get` on the same variable. The atomic `compareAndSet` method also has these memory consistency feature, as do the simple atomic arithmetic methods that apply to integer atomic variables.

Example of Basic Usage of `AtomicInteger`

```java
AtomicInteger atomicInteger = new AtomicInteger(0);
atomicInteger.accumulateAndGet(10, (x, y)->{return x + y;});
System.out.println(atomicInteger.get());
```

Example of Using Atomic Variable instead of Synchronized Methods

```java
public class AtomicCounter {
    private AtomicInteger c = new AtomicInteger(0);

    public void increment() {
        c.incrementAndGet();
    }

    public void decrement() {
        c.decrementAndGet();
    }

    public int value() {
        return c.get();
    }
}
```



**Concurrent Random Numbers**

The package `java.util.concurrent` includes a convenience class, `ThreadLocalRandom`, for applications that expect to use random numbers from multiple threads or `ForkJoinTasks`. 

For concurrent access, using `ThreadLocalRandom` instead of `Math.random()` results in less contention and better performance.

Example of `ThreadLocalRandom`

```java
int r = ThreadLocalRandom.current().nextInt(1, 10);
```



## Conclusion

In multi-threads programming, we may simultaneously access shared resources in multiple threads that may cause unpredicted results or corrupted values. For **thread-safe**, we need to consider two factors: thread interference and memory consistency errors. 

**Thread interference** can be solved by atomic access (common using exclusive locks). **Memory consistency errors** can be solved by establishing a happens-before relationship (that between reads and writes the same variable). 

We can simply use `synchronized` to solve the two thread-safe problems. But for **high concurrency** and thread-safe, we should use as few locks as possible, even have no locks. So we consider using a combination of explicit locks (reentrant locks), immutable objects, volatile variables, and atomic variables to solve the two thread-safe problems.

Locks may cause **liveness** problems: deadlock, starvation, and livelock. we can follow some coding principles to avoid these problems happens.

**Guarded blocks** common use for in threads cooperation. The most common example is the Producer-Consumer application.

**Executors** are for efficient thread creation and management.

**Concurrent collections** are high concurrency and thread-safe collection implementation.

## 

## References

[1] The Java Tutorial: A Short Course on the Basics

[2] [Difference between wait() and sleep()](https://stackoverflow.com/questions/1036754/difference-between-wait-and-sleep)

[3] [What does java.lang.Thread.interrupt() do?](https://stackoverflow.com/questions/3590000/what-does-java-lang-thread-interrupt-do)

[4] [Java - Understanding Happens-before relationship](https://www.logicbig.com/tutorials/core-java-tutorial/java-multi-threading/happens-before.html)

[5] [Memory Consistency - happens-before relationship in Java](https://stackoverflow.com/questions/16248898/memory-consistency-happens-before-relationship-in-java)