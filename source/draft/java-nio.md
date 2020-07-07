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

Modern operating systems offer sophisticated I/O services (such as readiness selection) for improving I/O performance and simplifying I/O. **Java Specification Request (JSR) 51** was created to address these capabilities.

JSR 51's description indicates that it provides APIs for scalable I/O, fast buffered binary and character I/O, regular expressions, and charset conversion. Collectively, these APIs are known as NIO. JDK 1.4 implemented NIO in terms of the following APIs:

- Buffers
- Channels
- Selectors
- Regular expressions
- Charsets

**Buffers**

Buffers are the foundation for NIO operations. Essentially, **NIO is all about moving data into and out of buffers**.

The process of a classic read operation like the following steps. The operating system issues a command to the disk controller to read a block of bytes from a disk into operating system buffer. Once this operation complete, the operating system copies the buffer contents to the buffer specified by the process when it issued a read() operation.

These are some inefficiency in classic I/O. Copying bytes from the operating system buffer to the process buffer isn't very efficient. It would be more performance to have the DMA controller copy directly to the process buffer, but there are two problems with this approach:

- The DMA controller typically cannot communicate directly with the user space in which the JVM process runs.
- Block-oriented device such as a DMA controller work with fixed-size data blocks.

Because of these problems, the operating system acts as an intermediary, tearing apart and recombining data as if switches between the JVM process and the DMA controller.

The data assembly/disassembly tasks can be made more efficient by letting the JVM process pass a list of buffer addresses to the operating system in a single call. The operating system then fills or drains these buffers in sequence, scattering data to multiple buffers during a reading or gathering data from several buffers during a write operation. This scatter/gather activity reduces the number of system calls.

JDK 1.4's `java.nio.Buffer` class abstracts the concept of a JVM process buffer.

**Channels**

Forcing a CPU to perform I/O tasks and wait for I/O completions is wasteful of this resource. Performance can be improved by offloading these tasks to DMA controllers so that the processor can get on with other work.

A channel serves as conduit for communicating (via the operating system) with a DMA controller to efficiently drain byte buffers to or fill byte buffers form a disk. JDK 1.4's `java.nio.channels.Channel` interface, its subinterfaces, and various classes implement the channel architecture.

**Selectors**

I/O is classified as block-oriented or stream-oriented. Reading from or writing to a file is an example of block-oriented I/O. In contrast, reading from the keyboard or writing to a network connection is an example of stream-oriented I/O.

Stream I/O is often slower than block I/O. Many operating systems allow streams to be configured to operate in nonblocking mode in which a thread continually checks available input without blocking when no input is available. The thread can handle incoming data or perform other tasks until data arrives.

This "polling for available input" activity can be wasteful, especially when the thread needs to monitor many input streams. Modern operating systems can perform this checking efficiently, which is known as readiness selection, and which is often built on top of nonblocking mode. The operating system monitors a collection of streams and returns an indication to the thread of which streams are ready to perform I/O. As a result, **a single thread can multiplex many active streams via common code and makes it possible.**

JDK 1.4 supports readiness selection by providing selectors, which are instances of the `java.nio.channels.Selector` class that can examine one or more channels and determine which channels are ready for reading or writing.

### NIO.2

JSR 51 specifies that NIO would introduce an improved file system interface, and to support asynchronous I/O and complete socket channel functionality. However, lack of time prevented these features from being included. JSR 203 was subsequently created to address these features, which introduced in JDK 7.

**Improved File System Interface**

The legacy File class suffers from various problems. For example, the renameTo() method doesn't work consistently across operating systems. The new file system interface fixes these and other problems.

**Asynchronous I/O**

Nonblocking mode improves performance by preventing a thread that performs a read or write operation on a channel from blocking until input is available or output has been fully written. However, it doesn't let an application determine if it can perform an operation without actually performing the operation. Nonblocking mode can't separate code that **checks for stream readiness** form the data-processing code without making your code significantly complicated.

Asynchronous I/O overcomes this problem by letting the thread initiate the operation and immediately proceed to other work. The thread specifies some kind of **callback function** that is invoked when the operation finishes.

**Completion of Socket Channel Functionality**

JDK 1.4 added the `DatagramChannel`, `ServerSocketChannel`, and `SocketChannel` classes to the `java.nio.channels` package. However, lack of time prevented these classes form supporting binding and option configuration. Also, channel-based multicast datagrams were not supported. JDK 7 added binding support and option configuration to the aforementioned classes. Also, it introduced a new `java.nio.channels.MulticastChannel` interface.

## Buffers

NIO is based on buffers, whose contents are sent to or received from I/O services via channels. This section introduces to NIO's buffer classes.

A buffer is an object that stores a fixed amount of data to be sent to or received from an I/O service. It sits between an application and a channel that writes the buffered data to the service or reads the data from the service and deposits it into the buffer.

Buffers are not safe for use by multiple concurrent threads. If a buffer is to be used by more than one thread then access to the buffer should be controlled by appropriate synchronization.

Buffers have four basic properties:

- Capacity. It is the number of elements it contains.
- Limit. It is the index of the first element that should not be read or written.
- **Position**. It is the index of the next element to be read or written. A buffer's position is never negative and is never greater than its limit.
- **Mark**. It's the index to which its position will be reset when the reset method is invoked.

```
0 <= mark <= position <= limit <= capacity
```

Buffer classes hierarchy

```
A-java.nio.Buffer
|----A-ByteBuffer
|----A-CharBuffer
|----A-DoubleBuffer
|----A-FloatBuffer
|----A-IntBuffer
|----A-LongBuffer
|----A-ShortBuffer
```

Methods of Buffer

- Abstract methods
  - abstract Object array()
  - abstract int arrayOffset()
  - abstract boolean hasArray()
  - abstract boolean isDirect()
  - abstract boolean isReadOnly()
- Return Buffer's Property
  - int capacity()
  - int limit()
  - int position()
  - boolean hasRemaining(). Tells whether there are any elements between the current position and the limit.
  - int remaining(). Returns the number of elements between the current position and the limit.
- Set Buffer's Property
  - **Buffer limit(int newLimit)**. Sets this buffer's limit.
  - **Buffer position(int newPosition)**. Sets this buffer's position.
  - **Buffer mark()**. Sets this buffer's mark at its position.
  - **Buffer reset()**. Resets this buffer's position to the previously-marked position.
- Clearing, flipping, and rewinding
  - **Buffer clear()**. Clears this buffer. It makes a buffer ready for a new sequence of channel-read or relative *put* operations. The position is set to zero, the limit is set to the capacity, and the mark is discard. Invoke this method before using a sequence of channel-read or put operation to fill this buffer. For example, `buf.clear(); in.read(buf);`
  - **Buffer flip()**. Flips this buffer. It makes a buffer ready for a new sequence of channel-write or relative *get* operations. The limit is set to the current position, the position is set to zero. If the mark is defined then it is discarded. After a sequence of channel-read or put operations, invoke this method to prepare for a sequence of channel-write or relative get operations. For example, `buf.put(magic); in.read(buf); buf.flip(); out.write(buf);`. This method is often used in conjunction with the compact method when transferring data from one place to another.
  - **Buffer rewind()**. Rewinds this buffer. It makes a buffer ready for re-reading the data that it already contains. The position is set to zero and the mark is discarded. Invoke this method before a sequence of channel-write or get operations, assuming that the limit has already bean set appropriately. For example, `out.write(buf); buf.rewind(); buf.get(array);`

**Buffer Creation**

ByteBuffer and the other primitive-type buffer classes declare various class methods for creating a buffer of that type.

Methods for creating ByteBuffer instances:

- ByteBuffer allocate(int capacity)
- ByteBuffer allocateDirect(int capacity)
- ByteBuffer wrap(byte[] array)
- ByteBuffer wrap(byte[] array, int offset, int length)

These methods show two ways to create a byte buffer: create the ByteBuffer object and allocate an internal array that stores capacity bytes or create the ByteBuffer object and use the specified array to store these bytes. For example:

```
ByteBuffer buffer = ByteBuffer.allocate(10);
byte[] bytes = new byte[200];
ByteBuffer buffer2 = ByteBuffer.wrap(bytes);
```

View Buffers

Buffers can manage data stored in other buffers. **View buffers** are create a buffer that manages another buffer's data. View buffers share the same internal array. Each buffer has its own position, limit and mark.

```
ByteBuffer buffer = ByteBuffer.allocate(10);
ByteBuffer bufferView = buffer.duplicate();
```

Read-only view buffers

```java
ByteBuffer buffer = ByteBuffer.allocate(10);
ByteBuffer bufferView = buffer.asReadOnlyBuffer();
```

**Buffer Writing and Reading**

Methods for ByteBuffer's writing and reading

- ByteBuffer put(byte b)
- ByteBuffer put(int index, byte b)
- Byte get()
- Byte get(int index)

**Flipping Buffers**

`buffer.flip()` equals `buffer.limit(buffer.position()).position(0)`. A example flipping buffer:

```java
buffer.put(s);
buffer.flip();
while (buffer.hasRemaining()){
    System.out.print(buffer.get());
}
buffer.clear();
```

**Compact Buffers**

The compact() method moves unwritten buffer data to the beginning of the buffer so that the next read() method call appends read data to the buffer's data instead of overwriting that data. Do this in case of a partial write.

```java
buf.clear();
while (in.read(buf) != -1){
    buf.flip();
    out.write(buf);
    buf.compact();
}
```

**Byte Ordering**

...

**Direct Byte Buffers**

...

## References

[1] Java I/O, NIO and NIO.2 by Jeff Friesen

[2] Java SE 8 API