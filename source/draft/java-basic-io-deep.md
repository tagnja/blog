Deeply Understanding Java IO

Based java SE 8

## Input Stream

### Input Stream methods

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

### What does Input Stream do?

`InputStream` only read by from front to back. It can skip some data to read. It can back to specified position to read.

`InputStream` can read bytes one by one or read some bytes once.

If there is no available data in `InputStream`, the calling read method will return -1.

`int read(byte b[])` => `int read(byte b[], int offset, int len)` => `abstract int read()`

`read()` can implement directly (e.g ByteArrayInputStream) or implement by calling the native method (e.g FileInputStream).

`InputStream` type

- ByteArrayInputStream: Byte array as data
- FileInputStream: File as data
- <s>StringBufferStream</s>: String as data. (Deprecated)
- SequenceInputStream: Logical concatenation of other input streams.
- PipedInputStream: A piped input stream should be connected to a piped output stream; the piped input stream then provides whatever data bytes are written to the piped output stream. 
- FilterInputStream: It contains some other input stream as data, and transforming data or providing additional functionality.
  - BufferedInputStream
  - PushbackInputStream
  - DataInputStream
  - LineNumberInputStream

