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
- Writer
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

`InputStream` can read bytes one by one or read some bytes once.

If there is no available data in `InputStream`, the calling one of the read methods will return -1.

`int read(byte b[])` => `int read(byte b[], int offset, int len)`, `abstract int read()`

`read()` methods can implement directly (e.g ByteArrayInputStream) or implement by calling the native method (e.g FileInputStream).

### InputStream Types

- `ByteArrayInputStream`: Byte array as data
- `FileInputStream`: File as data
- `ObjectInputStream`: Deserializes primitive data and objects previously written using an `ObjectOutputStream`.
  - `readObject()`
- `PipedInputStream`: A piped input stream should be connected to a piped output stream; the piped input stream then provides whatever data bytes are written to the piped output stream. 
  - `connect(PipedOutputStream src)`
- <s>`StringBufferStream`</s>(Deprecated): String as data.
- `SequenceInputStream`: Logical concatenation of other input streams.
  - `SequenceInputStream(InputStream s1, InputStream s2)`
- `FilterInputStream`: It contains some other input stream as data, and transforming data or providing additional functionality.
  - `BufferedInputStream`: read data from the internal buffer array rather than read data from input stream every time.
  - `PushbackInputStream`: unread one byte.
    - `unread(int byteValue)`
  - `DataInputStream`: read primitive Java data types.
    - `readInt()`
  - <s>`LineNumberInputStream`</s>(Deprecated): provides the added functionality of keeping track of the current line number.
    - `getLineNumber()`

## OutputStream

### OutputStream Methods

### What does Output Stream actually do?

### OutputStream Types

### Notes

if not implements Serializable interface, calling writeObject() will throw `java.io.NotSerializableException`

## Reader

## Writer

## Interfaces

## IO Exceptions

## File Paths

get File by file path in project.

get File by file path in file system.

get File by file path in project and when running JAR.

get File by file path in project and when running test.

## Questions

### InputStream vs Reader? What the real difference between them? Who has more efficient?

