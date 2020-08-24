---
title: JVM Run-Time Data Areas
date: 2020-06-19 15:03:08
categories: 
- 性能优化
tags: JVM
---



The Java Virtual Machine defines various run-time data areas that are used during execution of a program. Some of these data areas are created on Java Virtual Machine start-up and are destroyed only when the Java Virtual Machine exits. Other data areas are per thread. Per-thread data areas are created when a thread is created and destroyed when the thread exits. The data areas of JVM are logical memory spaces, and they may be not contiguous physical memory space. The following image shows the JVM run-time data areas:

<img class="img-center" src="https://taogenjia.com/img/jvm-runtime-data-areas/jvm-run-time-data-areas.png" />

## The `pc` Register

The Java Virtual Machine can support many threads of execution at once. Each JVM thread has its own `pc` (program counter) register. At any point, each JVM thread is executing the code of a single method, namely the current method for that thread. If that method is not `native`, the `pc` register contains the address of the JVM instruction currently being executed. If the method is `native`, the value of the `pc` register is undefined.

## Java Virtual Machine Stacks

Each JVM thread has a private JVM stack, created at the same time as the thread. A JVM stack stores frames. The JVM stack holds local variables and partial results, and plays a part in method invocation and return. 

Each frame in a stack stores the current method's local variable array, operand stack, and constant pool reference. A JVM stack may have many frames because until any method of a thread is finished it may call many other methods, and frames of these methods also store in the same JVM stack.

<img class="img-center" src="https://taogenjia.com/img/jvm-runtime-data-areas/stack-frame.png" />

Because the JVM stack is never manipulated directly except to push and pop frames, frames may be heap allocated. The memory for a JVM stack does not need to be contiguous.

**Frames**

A frame is used to store data and partial results, as well as to perform dynamic link, return values for method, and dispatch exceptions.

A new frame is created each time a method is invoked. A frame is destroyed when its method invocation completes, whether that completion is normal or abrupt. Frames are allocated from the JVM stack of the thread creating the frame. Each frame has its own array of local variables, operand stack, and a reference to the run-time constant pool of the class of the current method.

Only one frame for the executing method, is active at any point in a given thread of control. This frame is referred to as the `current frame`, and its method is known as the `current method`. The class in which the current method is defined is the `current class`. Operations on local variables and the operand stack are typically with reference to the current frame.

A frame ceases to be current if its method invokes another method or if its method completes. When a method is invoked, a new frame is created and becomes current when control transfers to the new method. On method return, the current frame passes back the result of its method invocation, to the previous frame.

A frame created by a thread is local to that thread and connot be referenced by any other thread.

## Heap

The JVM has a heap that is shared among all JVM threads. The heap is the run-time data area from which memory for all class instances and arrays is allocated.

The heap is created on virtual machine start-up. Heap storage for objects is reclaimed by an automatic storage management system (known as a garbage collector); objects are never explicitly deallocated. The JVM assumes no particular type of automatic storage management system, and the storage management technique may be chosen according to the implementor's system requirements. The memory for the heap does not need to be contiguous.

## Method Area

The JVM has a method area that is shared among all JVM threads. The method area is analogous to the storage area for compiled code of conventional language or analogous to the "text" segment in an operating system process. It stores per-class structures such as the run-time constant poll, field and method data, and the code for methods and constructors, including the special methods used in class and instance initialization and interface initialization.

The Method area is created on virtual machine start-up. Although the method area is logically part of the heap, simple implementations may choose not to either garbage collect or compact it. The method area may be of a fixed size or may be expanded as required. The memory for the method area does not need to be contiguous.

## Run-Time Constant Pool

A run-time constant pool is a per-class or per-interface run-time representation of the `constant_pool` table in a `class` file. It contains several kinds of constant, ranging from numeric literals known at compile-time to method and field references that must be resolved at run-time. The run-time constant pool serves a function similar to that of a symbol table for a conventional programming language, although it contains a wider range of data than a typical symbol table.

Each run-time constant pool is allocated from the JVM's method area. The run-time constant pool for a class or interface is constructed when the class or interface is created by the JVM.

References constant pools

- A symbolic reference to a class or interface is in `CONSTANT_Class_info` structure.
- A symbolic reference to a field of a class are in ``CONSTANT_Fieldref_info`` structure.
- A symbolic reference to a method of a class is derived from a `CONSTANT_Methodref_info` structure.
- A symbolic reference to a method of an interface is derived from a `CONSTANT_InterfaceMethodref_info` structure.

Literals constant pools

- A string literal is in `CONSTANT_String_info` structure
- Constant values are in `CONSTANT_Integer_info`, `CONSTANT_Float_info`, `CONSTANT_Long_info`, or `CONSTANT_Double_info` structures.

## Native Method Stacks

JVM may use native method stacks to support native methods. The native methods are written in a language other than the Java programming language. JVM implementations that cannot load native methods and that do not themselves rely on conventional stacks need not supply native method stack. If supplied, native method stacks are typical allocated per thread when each thread is created.



## How does A Program's Running Represent in JVM Memory Area

- Program start with the `main` method.
- Before execute the `main()`. 1) JVM creates a thread and allocates `pc register`, `JVM stack`, `native method stack` run-time memory area for the main thread. 2) JVM loading the class of `main()` and initializes the class static resources (that stored in `constant pool`). 3) create a new frame for `main()` method and construct the frame's structure. 4) update the pc register to refer to the first line of `main()`. 
- JVM executing `pc register` referred  instruction of the main thread.
- When we need to create an instance of a class in the `main()` method. 1) JVM loads the calling class, and initializes its static resources  2) JVM creates a new object in `heap`, and initializes its members, and constructs the object.
- When we calling a method of the new created object in the `main()`. 1) The JVM create a new frame in `JVM stack` for the calling method of  that object, and that frame becomes current frame. 2) update `pc register` to refer to that method. 3) JVM executing `pc register` referred instruction.

## Conclusion

Per-Thread Data Areas

- **The `pc` Register**. It contains the address of the JVM instruction currently being executed.
- **Java Virtual Machine Stacks**. It holds local variables and partial results.
- **Native Method Stacks**. To support native methods.

Threads-Shared Data Areas

- **Heap**. It contains all class instances and arrays.
- **Method Area**. It's part of `heap`. It stores per-class structures such as run-time constant poll, field and method data, and the code for methods and constructors.
- **Run-Time Constant Pool**. It's part of `method area`. It contains several kinds of constant, literals and reference constant.

## References

[1] The Java Virtual Machine Specification

[2] [JVM Stacks and Stack Frames](https://alvinalexander.com/scala/fp-book/recursion-jvm-stacks-stack-frames/)