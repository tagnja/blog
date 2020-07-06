Title: Understanding Java: NIO

**Content**

- Introduction
- NIO
  - Buffers
  - Selectors
  - Regular Expressions
  - Charsets
  - Formatters
- NIO.2
  - Improved Feil System Interface
  - Asynchronous I/O
  - Completion of Socket Channel Functionality
- References

This post we are going to talk about NIO and NIO.2 in Java. The NIO was introduced in Java 5. The NIO is missing several features, which were subsequently provided by NIO.2.

## Introduction

Input and output (I/O) facilities are fundamental parts of operating systems along with computer languages and their libraries.

Java initial suite of I/O APIs and related architecture are known as classic I/O. Because modern operating systems feature newer I/O paradigms, which class I/O doesn't support, new I/O (NIO) was introduced as part of JDK 1.4 to support them. Because lack of time, Some other NIO features being deferred to JDK 5 and JDK 7.

### Classic I/O

JDK 1.0 introduced I/O facilities for accessing the file system, accessing file content randomly, and streaming byte-oriented data between sources and destinations in a sequential manner.

### NIO

Modern operating systems offer sophisticated I/O services (such as readiness selection) for improving I/O performance and simplifying I/O. Java Specification Request (JSR) 51 was created to address these capabilities.

JSR 51's description indicates that it provides APIs for scalable I/O, fast buffered binary and character I/O, regular expressions, and charset conversion. Collectively, these APIs are known as NIO. JDK 1.4 implemented NIO in terms of the following APIs:

- Buffers
- Channels
- Selectors
- Regular expressions
- Charsets

**Buffers**

Buffers are the foundation for NIO operations. Essentially, NIO is all about moving data into and out of buffers.

The process of a classic read operation like the following steps. The operating system issues a command to the disk controller to read a block of bytes from a disk into operating system buffer. Once this operation complete, the operating system copies the buffer contents to the buffer specified by the process when it issued a read() operation.

These are some inefficiency in classic I/O. Copying bytes from the operating system buffer to the process buffer isn't very efficient. It would be more performance to have the DMA controller copy directly to the process buffer, but there are two problems with this approach:

- The DMA controller typically cannot communicate directly with the user space in which the JVM process runs.
- Block-oriented device such as a DMA controller work with fixed-size data blocks.

Because of these problems, the operating system acts as an intermediary, tearing apart and recombining data as if switches between the JVM process and the DMA controller.





### NIO.2



## References

[1] Java I/O, NIO and NIO.2 by Jeff Friesen