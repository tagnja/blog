Title: JVM specification: Run-Time Data Area

**Content**

- The `pc` Register
- Java Virtual Machine Stacks
- Heap
- Method Area
- Run-Time Constant Pool
- Native Method Stacks
- Frames

The Java Virtual Machine defines various run-time data areas that are used during execution of a program. Some of these data areas are created on Java Virtual Machine start-up and are destroyed only when the Java Virtual Machine exits. Other data areas are per thread. Per-thread data areas are created when a thread is created and destroyed when the thread exits.

The data areas of JVM are logical memory spaces, and they may be not contiguous physical memory space.

## The `pc` Register

The Java Virtual Machine can support many threads of execution at once. Each JVM thread has it won `pc` (program counter) register. At any point, each JVM thread is executing the code of a single method, namely the current method for that thread. If that method is not `native`, the `pc` register contains the address of the JVM instruction currently being executed. If the method is `native`, the value of the `pc` register is undefined.

## Java Virtual Machine Stacks

Each JVM thread has a private JVM stack, created at the same time as the thread. A JVM stack stores frames. The JVM stack holds local variables and partial results, and plays a part in method invocation and return. 

Each frame in a stack stores the current method's local variable array, operand stack, and constant pool reference. A JVM stack may have many frames because until any method of a thread is finished it may call many other methods, and frames of these methods also store in the same JVM stack.

<img class="img-center" src="stack-frame.png" />

Because the JVM stack is never manipulated directly except to push and pop frames, frames may be heap allocated. The memory for a JVM stack does not need to be contiguous.



## References

[1] The Java Virtual Machine Specification

[2] [JVM Stacks and Stack Frames](https://alvinalexander.com/scala/fp-book/recursion-jvm-stacks-stack-frames/)