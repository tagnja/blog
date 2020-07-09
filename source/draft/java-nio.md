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

### Buffer Classes

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

### Buffers in Depth

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

### Channel classes

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

### Channels in Depth

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

The following code is FileChannel usage example:

```java
RandomAccessFile raf = new RandomAccessFile("temp.txt", "rw");
FileChannel fc = raf.getChannel();
long pos = fc.position();
System.out.println("Position: " + pos);
System.out.println("Size: " + fc.size());
String msg = "This is a test message.";
ByteBuffer buffer = ByteBuffer.allocateDirect(msg.length() * 2);
buffer.asCharBuffer().put(msg);
fc.write(buffer);
fc.force(true);
System.out.println("Position: " + fc.position());
System.out.println("Size: " + fc.size());
buffer.clear();
fc.position(pos);
fc.read(buffer);
buffer.flip();
while (buffer.hasRemaining()){
    System.out.print(buffer.getChar());
}
```

Locking Files

The ability to lock all or part of a file was an important but missing feature from Java until Java 1.4 arrived. This capability lets a JVM process prevent other processes form accessing all or part of a file until it's finished with the entire file or part of the file.

Although an entire file can be locked, it's often desirable to lock a smaller region. For example, a database management system might lock individual table rows.

Locks that are associated with files are known as file locks. Each file lock starts at a certain byte position in the file and has a specific length (in bytes) from this position.

There are two kinds of file locks: exclusive and shared.

There are some important for file locking:

- When an operating system doesn't support shared locks, a shared lock request is quietly promoted to a request for an exclusive lock.
- Locks are applied on a per-file basis. The are not applied on a per-thread or per-channel basis.

FileChannel declares four methods for obtaining exclusive and shared locks:

- FileLock lock()
- FileLock lock(long position, long size, boolean shared)
- FileLock tryLock()
- FileLock tryLock(long position, long size, boolean shared)

A FileLock instance is associated with a FileChannel instance but the file lock represented by the FileLock instance associates with the underlying file and not with the file channel. Without care, you can run into conflicts (and possibly even a deadlock) when you don't release a file lock after you're finished using it. The following code shows the FileLock usage:

```java
FileLock lock = FileChannel.lock();
try {
    // interact with the file channel
} catch (IOException ioe){
    // handle the exception
} finally {
    lock.release();
}
```



Mapping Files into Memory

FileChannel declares a map() method that lets you create a virtual memory mapping between a region of an open file and a `java.nio.MappedByteBuffer` instance that wraps itself around this region. This mapping mechanism offers an efficient way to access a file because no time-consuming system calls are needed to perform I/O. The map method is:

```java
MappedByteBuffer map(FileChannel.MapMode mode, long position, long size)
```

`FileChannel.MapMode` enumerated type:

- READ_ONLY
- READ_WRITE
- PRIVATE

Changes made to the resulting buffer will eventually be propagated to the file. They might not be made visible to other programs that have mapped the same file.

The specified mapping mode is constrained by the invoking FileChannel object's access permissions. For example, if the file channel was opened as a read-only channel, and if you request READ_WRITE mode, map() will throw `NonWritableChannelException`.

Invoke `MappedByteBuffer`'s `isReadOnly()` method to determine whether or not you can modify the mapped file.

The position and size parameters define the start and extent of the mapped region.

There is no unmap() method. Once a mapping is established, it remains until the MappedByteBuffer object is garbage collected (or the application exits).

The following code shows A MappedByteBuffer example:

```java
RandomAccessFile raf = new RandomAccessFile("temp.txt", "rw");
FileChannel fc = raf.getChannel();
long size = fc.size();
System.out.println("Size: " + size);
MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, 0,
                              size);
mbb.clear();
while (mbb.remaining() > 0) {
    System.out.print((char) mbb.get());
}
System.out.println();
System.out.println();

String msg = "hello";
mbb.clear();
mbb.asCharBuffer().put(msg);
while (mbb.hasRemaining()) {
    fc.write(mbb);
}
fc.close();
```



Transferring Bytes Among Channels

To optimize the common practice of performing bulk transfers, two methods have bean added to FileChnnel that avoid the need for intermediate buffers:

- long transferFrom(ReadableByteChannel src, long position, long count)
- long transferTo(long position, long count, WritableByteChannel target)

There is a example of transferring between channels:

```java
try (FileInputStream fis = new FileInputStream("temp.txt")){
    FileChannel inChannel = fis.getChannel();
    WritableByteChannel outChannel = Channels.newChannel(System.out);
    inChannel.transferTo(0, inChannel.size(), outChannel);
} catch (IOException ioe){
    System.out.println("I/O error: " + ioe.getMessage());
}
```



**Socket Channels**

Socket declares a `SocketChannel getChannel()` method for returning a socket channel instance, which describes an open connection to a socket. Unlike sockets, socket channels are selectable and can function in nonblocking mode. These capabilities enhance the scalability and flexibility of large applications.

Socket channels are describes by the `java.nio.channels` package's abstract ServerSocketChannel, SocketChannel, and DatagramChannel classes. Each class ultimately extends `SelectableChannle` and `InterruptibleChannel`, making socket channels selectable and interruptible.

SelecableChannel offers the following methods to enable blocking or nonblocking:

- SelectableChannel configureBlocking(boolean block)
- boolean isBlocking()
- Object blockingLock()

To enable nonblocking sockets:

```java
ServerSocketChannel ssc = ServerSocketChannel.open();
ssc.configureBlocking(false);
```

The blockingLock() method lets you prevent other threads from changing a socket channel's blocking/nonblocking status.

```java
ServerSocketChannel ssc = ServerSocketChannel.open();
SocketChannel sc = null;
Object lock = ssc.blockingLock();
synchronized(lock){
    boolean blocking = ssc.isBlocking();
    ssc.configureBlocking(false);
    sc = ssc.accept();
    ssc.configureBlocking(blocking)
}
```

Methods of ServerSocketChannel:

- static ServerSocketChannel open(). Attempt to open a server-socket channel, which is initially unbound; it must be bound to a specific address via one of its peer socket's bind() methods before connections can be accepted.
- ServerSocket socket()
- SocketChannel accept(). Accept the connection made this channel's socket. If this channel is nonblocking, it immediately returns null when there are no pending connections or returns a socket channel that represents the connection. Otherwise, when the channel is blocking, it blocks.

An example of ServerSocketChannel as shown below:

```java
ServerSocketChannel ssc = ServerSocketChannel.open();
ssc.socket().bind(new InetSocketAddress(9999));
ssc.configureBlocking(false);
String msg = "Local address: " + ssc.socket().getLocalSocketAddress();
ByteBuffer buffer = ByteBuffer.wrap(msg.getBytes());
while (true){
    System.out.print(".");
    SocketChannel sc = ssc.accept();
    if (sc != null){
        System.out.println();
        System.out.println("Received connection from " +
        sc.socket().getRemoteSocketAddress());
        buffer.rewind();
        sc.write(buffer);
        sc.close();
	} else {
		try{
			Thread.sleep(100);
		}catch (InterruptedException ie){
			assert false; // shouldn't happen
		}
    }
}
```

Methods of SocketChannel:

- static SocketChannel open()
- static SocketChannel open(InetSocketAddress remoteAddr)
- Socket socket()
- boolean connect(SocketAddress remoteAddr)
- boolean isConnectionPending()
- booean finishConnect()
- boolean isConnected()

```java
SocketChannel sc = SocketChannel.open();
sc.connect(new InetSocketAddress("localhost", 9999));
while (!sc.finishConnect()){
    System.out.println("waiting to finish connection");
}
ByteBuffer buffer = ByteBuffer.allocate(200);
buffer.asCharBuffer().put("hello at " + new Date());
// send
sc.write(buffer);
// receive
while (sc.read(buffer) >= 0){
    buffer.flip();
    while (buffer.hasRemaining()){
        System.out.print((char) buffer.get());
    }
    buffer.clear();
}
sc.close();
```



**Pipes**

Pipe describes a pair of channels that implement a unidirectional pipe, which is a conduit for passing data in one direction between two entities, such as two file channels or two socket channels. Pipe is analogous to the `java.io.PipedInputStream` and `PiepedOutputStream`.

Pipe declares nested SourceChannel and SinkChannel classes that serve as readable and writable byte channels, respectively. Pipe also declares the following methods:

- static Pipe open()
- SourceChannel source()
- SinkChannel sink()

Pipe can be used to pass data within the same JVM. Pipes are ideal in producer/consumer scenarios because of encapsulation: you can use the same code to write data to files, sockets, or pipes depending on the kind of channel presented to the pipe.

The following code shows a producer/consumer example:

```java
final int BUFSIZE = 10;
final int LIMIT = 3;
final Pipe pipe = Pipe.open();
Thread sender = new Thread(() -> {
    WritableByteChannel src = pipe.sink();
    ByteBuffer buffer = ByteBuffer.allocate(BUFSIZE);
    for (int i = 0; i < LIMIT; i++) {
        buffer.clear();
        for (int j = 0; j < BUFSIZE; j++) {
            double random = Math.random();
            buffer.put((byte) (random * 256));
            System.out.println("CLIENT Send: " + (byte) (random * 256));
        }
        buffer.flip();
        try {
            while (src.write(buffer) > 0) ;
        } catch (IOException ioe) {
            System.err.println(ioe.getMessage());
        }
    }
    try {
        src.close();
    } catch (IOException ioe) {
        ioe.printStackTrace();
    }
});
Thread receiver = new Thread(() -> {
    ReadableByteChannel dst = pipe.source();
    ByteBuffer buffer = ByteBuffer.allocate(BUFSIZE);
    try {
        while (dst.read(buffer) >= 0) {
            buffer.flip();
            while (buffer.remaining() > 0) {
                System.out.println("SERVER Receive: " + (buffer.get() & 255));
            }
            buffer.clear();
        }
    } catch (IOException ioe) {
        System.err.println(ioe.getMessage());
    }
});
sender.start();
receiver.start();
```



## Selectors

Nonblocking mode doesn't let an application determine if it can perform an operation without actually performing the operation. 

The operating system is instructed to observe a group of streams and return some indication of which streams are ready to perform a specific operation (such as read). This capability lets a thread multiplex a potentially huge number of active streams by using the readiness information provided by the operating system.

Selectors let you achieve readiness selection in a Java context.

A selector is an object created form a subclass of the abstract `java.nio.channels.Selector` class. The selector maintains a set of channels that it examines to determine which channels are ready for reading, writing, completing a connection sequence, accepting another connection, or some combination of these tasks. The actual work is delegated to the operating system via POSIX select() or similar system call.





---



- Asynchronous I/O

## Summary

Selectable

Interruptable



## References

[1] Java I/O, NIO and NIO.2 by Jeff Friesen

[2] Java SE 8 API