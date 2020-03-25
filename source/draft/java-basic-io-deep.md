Deeply Understanding Java IO

Based java SE 8

Content

- InputStream
  - InputStream Methods
  - What does Input Stream actually do?
  - InputStream Types
- OutputStream
  - OutputStream Methods
  - What does Output Stream actually do?
  - OutputStream Types
  - Notes
- Reader
  - Reader Methods
  - What does Reader actually do?
  - Reader Types
- Writer
  - Writer Methods
  - What does Writer actually do?
  - Writer Types
- Interfaces
- IO Exceptions
- File Paths
- Questions
- Summary

## Input Stream

### Input Stream Methods

- `int available()`: return available to read the remaining byte size.
- `void close()`: close the input stream.
- `void mark(int readlimit)`:  marks the current position in this input stream. 
  - `readlimit`: the number of bytes that can be read from the stream after setting the *mark* before the *mark* becomes invalid. Note the parameter for some subclass of `InputStream` has no meaning.
- `boolean markSupported()`: return if this input stream supports the mark and reset methods.
- `int read()`: reads the next byte of data.
- `int read(byte[] b)`: reads some bytes from input stream and stores them into buffer array b.
- `int read(byte[] b, int off, int len)`: reads up to `len` bytes of data from input stream into array of bytes.
- `void reset()`: repositions this stream to the position at mark (default 0)
- `long skip(long n)`: skips bytes of data.

### What does Input Stream actually do?

`InputStream` only read by from front to back. It can skip some data to read. It can back to specified position to read.

`InputStream` can read bytes one by one or read some bytes into byte array once.

If there is no available data in `InputStream`, the calling one of the read methods will return -1.

`int read(byte b[])` => `int read(byte b[], int offset, int len)`, `abstract int read()`

`read()` methods can implement directly (e.g ByteArrayInputStream) or implement by calling the native method (e.g FileInputStream).

### InputStream Types

- `ByteArrayInputStream`: Byte array as data
  - `ByteArrayInputStream(byte[] b)`
- `FileInputStream`: File as data
  - `FileInputStream(File file)`
- `ObjectInputStream`: Deserializes primitive data and objects previously written using an `ObjectOutputStream`.
  - `ObjectInputStream(InputStream in)`
  - `readObject()`
- `PipedInputStream`: A piped input stream should be connected to a piped output stream; the piped input stream then provides whatever data bytes are written to the piped output stream. 
  - `PipedInputStream()`
  - `connect(PipedOutputStream src)`
- <s>`StringBufferStream`</s>(Deprecated): String as data.
  - `StringBufferInputStream(String s)`
- `SequenceInputStream`: Logical concatenation of other input streams.
  - `SequenceInputStream(InputStream s1, InputStream s2)`
- `FilterInputStream`: It contains some other input stream as data, and transforming data or providing additional functionality.
  - `BufferedInputStream`: read data from the internal buffer array rather than read data from input stream every time.
    - `BufferedInputStream(InputStream in)`
  - `PushbackInputStream`: unread one byte.
    - `PushbackInputStream(InputStream in)`
    - `unread(int byteValue)`
  - `DataInputStream`: read primitive Java data types.
    - `DataInputStream(InputStream in)`
    - `readInt()`
  - <s>`LineNumberInputStream`</s>(Deprecated): provides the added functionality of keeping track of the current line number.
    - `LineNumberInputStream(InputStream in)`
    - `getLineNumber()`

## OutputStream

### OutputStream Methods

- `void close()`: close output stream and release resources.
- `void flush()`: Flushes output stream and forces buffered bytes to be written out.
- `void write(byte[] b)`: Writes byte array.
- `void write(byte[] b, int offset, int len)`: Writes `len` bytes from the byte array starting at offset.
- `void write(int b)`: Writes the specified byte.

### What does Output Stream actually do?

Write one byte or some bytes data to output stream.

If output stream doesn't call the flush or close method, some data written in buffer array may don't write to the stream.

Some output streams have the buffer array, others not.

### OutputStream Types

- `ByteArrayOutputStream`: Writes data to a byte array. retrieve data using `toByteArray()` and `toString()`.
  - `toByteArray()`
  - `toString()`
- `FileOutputStream`: Writes data to a File or FileDescriptor.
  - `FileOutputStream(File file)`
- `ObjectOutputStream`: Writes primitive data types and java objects to an OuputStream.
  - `ObjectOutputStream(OutputStream out)`
  - `writeObject(Object obj)`
  - `writeInt(int val)`
- `PipedOutputStream`: Sending data to pipe.
- `FilterOutputStream`: Sit on top of an already existing output stream. It transforming the data or  providing additional functionality.
  - `BufferedOutputStream`: Writes data to buffer array. Avoid write data to stream every time.
    - `BufferedOutputStream(OutputStream out)`
  - `DataOutputStream`: Write primitive Java data types to output stream.
    - `writeChars(String s)`
    - `writeInt(int val)`
  - `PrintStream`: Print various values to output stream.
    - `println(String s)`
    - `write(int b)`

### Notes

if not implements Serializable interface, calling writeObject() will throw `java.io.NotSerializableException`

## Reader

### Reader Methods

- `abstract void close()`
- `void mark(int readAheadLimit)`
- `boolean markSupported()`
- `int read()`
- `int read(char[] cbuf)`
- `abstract int read(char[] cbuf, int off, int len)`
- `int read(CharBuffer target)`
  - `java.nio.CharBuffer target` is using for read characters.
- `boolean ready()`: return whether this stream is ready to be read. It's same with `InputStream.available()`
- `void reset()`
- `long skip(long n)`

### What does Reader actually do?

Read one character or some characters into char array.

### Reader Types

Description like `InputStream`

- `BufferedReader`
  - `BufferedReader(Reader in)`
  - `readLine()`
- `LineNumberReader`
  - `LineNumberReader(Reader in)`
  - `readLine()`
  - `getLineNumber()`
- `CharArrayReader`
  - `CharArrayReader(char c[])`
- `Abstract FilterReader`
- `PushbackReader`
  - `PushbackReader(Reader in)`
  - `unread(int val)`
- `InputStreamReader`
  - `InputStreamReader(InputStream in)`
- `FileReader`
  - `FileReader(File file)`
- `PipedReader`
  - `connect(PipedWriter src)`
- `StringReader`
  - `StringReader(String s)`

## Writer

### Writer Methods

### What does Writer actually do?

### Writer Types

## Standard Streams

System.out

System.in

System.err

## Interfaces

## IO Exceptions

## File Paths

get File by file path in project.

get File by file path in file system.

get File by file path in project and when running JAR.

get File by file path in project and when running test.

## Questions

### InputStream vs Reader? What the real difference between them? Who has more efficient?

Stream is operating bytes, and reader or writer is operating characters. Writing streams of raw bytes such as image data using OutputStream. Writing streams of characters using Writer.

## Summary

### Stream Types

- Byte or Char Array
- File
- Object
- Piped
- Sequence: 
- Filter
  - Buffered: Operating with buffer array.
  - DataInput, DataOutput: Operating with primitive Java types
  - Pushback: Add unread functionality.
  - Print: 
  - LineNumber

### Functions

InputStream or Reader: read data from somewhere, e.g byte or char array, file, pipe. It can read single unit data from the stream to return or read multiple units data from the stream to store into an array every time.

OutputStream or Writer: write data to somewhere, e.g byte or char array, file, pipe. 

