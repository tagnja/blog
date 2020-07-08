Title: Understanding Java: NIO

**Content**

- Introduction
- NIO
  - Buffers
  - Channels
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

The Hierarchy of Buffer Classes 

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
- Get Buffer's Property
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

Nonbyte primitive types except for Boolean are composed of several bytes. Each value of one of these multibyte types is stored in a sequence of contiguous memory locations. However, the order of these bytes can differ from operating system to operating system.

For example, consider 32-bit long integer 0x10203040. This value's four bytes could be stored in memory (from low address to high address) as 10, 20, 30, 40; this arrangement is known as big endian order (the most-significant byte, the "big" end, is stored at the lowest address). Alternatively, these bytes could be stored as 40, 30, 20, 10; this arrangement is known as little endian order (the least-significant byte, the "little" end, is stored at the lowest address).

Java provides the `java.nio.ByteOrder` class to help you deal with byte-order issues when writing/reading multibyte value to/from a multibyte buffer. ByteOrder declares a ByteOrder `nativeOrder()` method that returns the operating system's byte order as a ByteOrder instance. This instance is one of ByteOrder's BIG_ENDIAN and LITTLE_ENDIAN constants.

- static ByteOrder BIG_ENDIAN
- static ByteOrder LITTLE_ENDIAN
- ByteOrder nativeOrder()

**ByteBuffer its default byte order is always big endian**, even when the underlying operating system's byte order is little endian. Because Java's default byte order is also big endian, which lets classfiles and serialized objects store data consistently across Java virtual machines.

Big endian default can impact performance on little endian operating systems, ByteBuffer also declares a **`ByteBuffer order(ByteOrder bo)` method to change the byte buffer's byte order**.

Although it may seem unusual to change the byte order of a byte buffer, this method is useful because ByteBuffer also declares several convenience methods for writing and reading multibyte values, such as `ByteBuffer putInt(int value)` and `int getInt()`. These convenience methods write these values according to the byte buffer's current byte order.

**Direct Byte Buffers**

Operating systems can directly access the address space of a process. For example, an operating system could directly access a JVM process's address space to perform a data transfer operation based on a byte array. However, a JVM might not store the array of bytes contiguously or its garbage collector might move the byte array to another location. Because of these limitations, direct byte buffer were created.

A direct byte buffer is a byte buffer that interacts with channels and native code to perform I/O. The direct byte buffer attempts to store byte elements in a memory area that a channel uses to perform direct (raw) access via native code that tells the operating system to drain or fill the memory area directly.

Direct byte buffers are the most efficient means performing I/O on the JVM. Although you can also pass non-direct byte buffers to channels, a performance problem might arise because non-direct byte buffers are not always to serve as the target of native I/O operations.

Although direct byte buffers are optimal for I/O, a direct byte buffer can be expensive to create because memory extraneous to the JVM's heap will need to be allocated by the operating system, and setting up/tearing down this memory might take longer than when the buffer way located within the heap.

After your code is working and should you want to experiment with performance optimization, you can easily obtain a direct byte buffer by invoking ByteBuffer's `allocateDirect()` method.



## Channels

Channels partner with buffers to achieve high-performance I/O.

A Channel is an object that represents an open connection to a hardware device, a file, a network socket, an application component, or another entity that's capable of performing writes, reads, and other I/O operations. Channels efficiently transfer data between byte buffers and operating system-based I/O service sources or destinations.

**Channel classes**

The Hierarchy of Channel Classes 

```
I-Channel
|----I-ReadableByteChannel
|--------I-ScatteringByteChannel
|----I-WritableByteChannel
|--------I-GatheringByteChannel
|--------I-ByteChannel
|------------I-SeekableByteChannel
|----I-InterruptibleChannel
|--------A-AbstractInterruptibleChannel
|------------A-FileChannel
|------------A-SelectableChannel
|----------------A-AbstractSelectableChannel
|--------------------A-DatagramChannel
|--------------------A-ServerSocketChannel
|--------------------A-SocketChannel
|--------------------A-Pipe.SinkChannel
|--------------------A-Pipe.SourceChannel
|----I-AsynchronousChannel
|--------I-AsynchronousByteChannel
|--------A-AsynchronousFileChannel
|--------A-AsynchronousServerSocketChannel
|--------A-AsynchronousSocketChannel
|----I-NetworkChannel
|--------I-MulticastChannel
```

All channels are instances of classes that ultimately implement the `java.nio.channels.Channel` interface. The `Chennel` declares the following methods:

- void close()
- boolean isOpen()

To support I/O, channel is extended by the `WritableByteChannel` and `ReadableByteChannel` interface.

- WritableByteChannel declares an `abstract int write(ByteBuffer buffer)` method that writes a sequence of bytes from buffer to the current channel.
- ReadableByteChannel declares an `abstract int read(ByteBuffer buffer)` method that reads bytes from current channel into buffer.

A channel whose class implements only WritableByteChannel or ReadableByteChannel is unidirectional.

InterruptibleChannel interface describes a channel that can be asynchronous closed and interrupted.

NIO's designers chose to shut down a channel when a blocked thread is interrupted because they couldn't find a way to reliably handle interrupted I/O operations in the same manner across operating systems. The only way to guarantee deterministic behavior was to shut down the channel.

Obtain a channel

There are two ways to obtain a channel:

- The `java.nio.channels` package provides a `Channels` utility class that offers two methods for obtaining channels from streams.
  - `WritableByteChannel newChannel(OutputStream outputStream)`
  - `ReadableByteChannel newChannel(InputStream inputStream)`
- Various classic I/O classes have been retrofitted to support channel creation.
  - `java.io.RandomAccessFile`'s `FileChannel getChannel()` method.
  - `java.net.Socket`'s `SocketChannel getChannel()` method. 

For example, obtain channels from standard I/O streams:

```java
ReadableByteChannel src = Channels.newChannel(System.in);
WritableByteChannel dest = Channels.newChannel(System.out);
ByteBuffer buffer = ByteBuffer.allocateDirect(2048);
while (src.read(buffer) != -1){
    buffer.flip();
    dest.write(buffer);
    buffer.compact();
}
buffer.flip();
while (buffer.hasRemaining()){
    dest.write(buffer);
}
```

**Scatter/Gather I/O**

Channels provide the ability to perform a single I/O operation across multiple buffers. This capability is known as scatter/gather I/O (and is also known as vectored I/O).

In the context of a write operation, the contents of several buffers are gathered in sequence and then sent through the channel to a destination. In the context of a read operation, the contents of a channel are scattered to multiple buffers in sequence.

Modern operating systems provide APIs that support vectored I/O to eliminate (or at least reduce) system calls or buffer copies, and hence improve performance.

Java Provides the `java.nio.channels.ScatteringByteChannel` interface to support scattering and `GatheringByteChannel` interface to support gathering.

ScattheringByteChannel offers the following methods:

- long read(ByteBuffer[] buffers, int offset, int length)
- long read(ByteBuffer[] buffers)

GatheringByteChannel offers the following methods:

- long write(ByteBuffer[] buffers, int offset, int length)
- long write(ByteBuffer[] buffers)

**File Channels**

RandomAccessFile, FileInputStream, and FileOutputStream provide getChannel() method for returning a file channel instance, which describes an open connection to a file.

The abstract `java.nio.channels.FileChannel` class describes a file channel. This class implements the InterruptibleChannel, ByteChannel, GatheringByteChannel, and ScatteringByteChannel interfaces.

Unlike buffers, which are not thread-safe, file channels are thread-safe.

A file channel maintains a current position into the file, which FileChannel lets you obtain and change. 

Methods of FileChannel:

- void force(boolean metadata)
- long position()
- FileChannel position(long newPosition)
- int read(ByteBuffer buffer)
- int read(ByteBuffer dst, long position)
- long size()
- FileChannel truncate(long size)
- int write(ByteBuffer buffer)
- int write(ByteBuffer src, long position)

FileChannel objects support the concept of a current file position, which determines the location where the next data item will be read from or written to.



**Socket Channels**

**Pipes**



------------------

- Selectors
- Asynchronous I/O





## References

[1] Java I/O, NIO and NIO.2 by Jeff Friesen

[2] Java SE 8 API