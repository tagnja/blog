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
- Reader
- Writer
- Interfaces
- File Paths
- Summary
- Questions

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
- `FileOutputStream`: Writes data to a File or `FileDescriptor`.
  - `FileOutputStream(File file)`
- `ObjectOutputStream`: Writes primitive data types and java objects to an `OuputStream`.
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

if not implements Serializable interface, calling `writeObject()` will throw `java.io.NotSerializableException`

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

Read one character to return or read some characters into char array from somewhere, e.g char array, file, pipe.

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

- `Writer append(char c)`
- `Writer append(CharSequence csq)`
  - Same with `write(String str)`, but it can chained call, and String parameter can be null.
- `Writer append(CharSequence csq, int start, int end)`
- `abstract void close()`
- `abstract void flush()`
- `void write(char[] cbuf)`
- `abstract void write(char cbuf, int offset, int len)`
- `void write(int c)`
- `void write(String str)`
- `void write(String str, int offset, int len)`

### What does Writer actually do?

Writes one character or write some characters to somewhere, e.g char array, file, pipe.

### Writer Types

- `BufferedWriter`
  - `BufferedWriter(Writer out)`
  - `void newLine()`
- `OutputStreamWriter`
  - `OutputStreamWriter(OutputStream out, Charset cs)`
- `FileWriter`
  - `FileWriter(File file)`
- `StringWriter`
  - `StringBuffer getBuffer()`
- `CharArrayWriter`
  - `String toString()`
  - `char[] toCharArray()`
- `abstract FilterWriter`
- `PipedWriter`
- `PrintWriter`
  - `PrintWriter(OutputStream out)`
  - `PrintWriter(Writer out)`
  - `void println(String s)`

## Standard Streams

`java.lang.System`

- `PrintStream out`
- `InputStream in`
- `PrintStream err`

## Interfaces

`java.lang.AutoClosable`: Auto close resources.

- `void close()`: This method is invoked automatically on objects managed by the try-with-resources statement.

`Closeable`: resources can be closed.

- `void close()`

`DataInput`: provide reading data in any of the Java primitive types.

- `boolean readBoolean()`
- `int readInt()`
- ...

`DataOutput`: provide writing data in any of the Java primitive types.

- `void writeBoolean(int b)`
- `void writeInt(int val)`
- ...

`Externalizable`: extends `Serializable` interface. It using for object serialization.

- `void readExternal(ObjectInput in)`
- `void writeExternal(ObjectOutput out)`

`FileFilter`: It is a functional interface and can be used for a lambda expression or method reference. Filter files.

- `boolean accpet(File pathname)`

`FileNameFilter`: It is a functional interface and can be used for a lambda expression or method reference. Filter file names.

- `boolean accept(File dir, String name)`

`Flushable`: data can be flushed.

- `void flush()`: Flushes buffered output to the underlying stream.

`ObjectInput`: extends `DataInput` interface. Allows reading object.

- `Object readObject()`

`ObjectInputValidation`: Callback interface to allow validation of objects within a graph. It has no implemented subclasses.

`ObjectOutput`: extends `DataOutput` interface. Allows writing object.

- `void writeObject(Object obj)`

`ObjectStreamConstants`: Constants written into the Object Serialization Stream.

`Serializable`: Allows class serializability. It has no methods.

## File

### File

- Fields
  - `static String pathSeparator`: system dependent path separator, e.g `;`.
  - `static String separator`: system dependent name separator, e.g `/`.
- Constructors
  - `File(File parent, String child)`, `File(String pathname)`, `File(String parent, Sring child)`, `File(URI uri)`
- File Permission
  - `boolean canExecute()`, `boolean canRead()`, `boolean canWrite()`
  - `boolean setExecutable(boolean executable)`, `boolean setWritable(boolean writable)`
- File Handling
  - `static File createNewFile() `, `static File createTempFile(String prefix, String suffix, File directory)`, `boolean mkdir()`, `boolean mkdirs()`, `renameTo(File dest)`
  - `boolea delete()`, `boolean deleteOnExit()`
  - `int compareTo(File pathname)`: compares two abstract pathnames lexicographically.
  - `String[] list()`, `String[] list(FilenameFilter filter)`, `File[] listRoots()`
  - `Path toPath()`, `URI toURI()`, `URL toURL()`
- File Information
  - `boolean exists()`
  - `isAbsolute()`, `isDirectory()`, `isFile()`, `isHidden()`
  - `String getAbsolutePath()`, `String getCanonicalPath()`, `String getName()`, `File getParentFile()`, `String getPath()`
  - `long getFreeSpace()`, `long getTotalSpace()`, `long getUsableSpace()`, `long lastModified()`, `long length()`

### FileDescriptor

> Whenever a file is opened, the operating system creates an entry to represent this file and stores its information. Each entry is represented by an integer value and this entry is termed as file descriptor. [2]

> Basically, Java class `FileDescriptor` provides a handle to the underlying machine-specific structure representing an open file, an open socket, or another source or sink of bytes. [2]

> The applications should not create FileDescriptor objects, they are mainly used in creation of `FileInputStream` or `FileOutputStream` objects to contain it. [2]

### File Paths

#### Get file, absolute filepath and inputStream in classpath, e.g `src/resources/test.txt`

You can get InputStream by using ClassLoader.getResourceAsStream() method.

```java
InputStream in = getClass().getClassLoader().getResourceAsStream("test.txt");
```

You can get file object and file path by using ClassLoader get URL, then get them.

```java
URL url = getClass().getClassLoader().getResource("test.txt");
File file = new File(url.getFile());
String filepath = file.getAbsolutePath();
```

Difference between `getResource()` methods: 

```java
getClass().getClassLoader().getResource("test.txt") //relative path
getClass().getResource("/test.txt")); //note the slash at the beginning
```

#### Get file, absolute file path and inputStream in JAR, e.g src/resources/test.txt

> This is deliberate. The contents of the "file" may not be available as a file. Remember you are dealing with classes and resources that may be part of a JAR file or other kind of resource. The classloader does not have to provide a file handle to the resource, for example the jar file may not have been expanded into individual files in the file system. [3]

> Anything you can do by getting a java.io.File could be done by copying the stream out into a temporary file and doing the same, if a java.io.File is absolutely necessary. [3]

Result: You can get input stream and File by ClassLoader, but you can't get right usable absolute file path. If you want get a usable absolute path in JAR, you can copy resource file stream to create a new temporary file, then get absolute file path of the temporary file.

```java
File file = null;
String resource = "/test.txt";
URL res = getClass().getResource(resource);
if (res.getProtocol().equals("jar")) {
    try {
        InputStream input = getClass().getResourceAsStream(resource);
        file = File.createTempFile("tempfile", ".tmp");
        OutputStream out = new FileOutputStream(file);
        int read;
        byte[] bytes = new byte[1024];

        while ((read = input.read(bytes)) != -1) {
            out.write(bytes, 0, read);
        }
        out.close();
        file.deleteOnExit();
    } catch (IOException ex) {
        Exceptions.printStackTrace(ex);
    }
} else {
    //this will probably work in your IDE, but not from a JAR
    file = new File(res.getFile());
}

System.out.println(file.getAbsolutePath());
```



## Summary

### Streams Function

`InputStream` or `Reader` it read data from somewhere, e.g byte or char array, file, pipe. It can read single-unit data from the stream to return or read multiple units data from the stream to store into an array every time. **`read()` methods** can implement by java code or implements by calling native method. **Filter streams** use the **Decorator Pattern** to add additional functionality (e.g unread) that implements by internal buffer array to other stream objects.

`OutputStream` or `Writer` it write data to somewhere, e.g byte or char array, file, pipe. 

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

### Others

`class java.nio.CharBuffer` using in method `int read(CharBuffer target)`

`interface java.lang.CharSequence` using in method `Writer append(CharSequence csq)`

## Questions

### InputStream vs Reader? What the real difference between them? Who has more efficient?

Stream is operating bytes, and reader or writer is operating characters. Writing streams of raw bytes such as image data using OutputStream. Writing streams of characters using Writer.

### CharSequence vs String

A `CharSequence` is a readable sequence of `char` values. You can call `chars()` method get the InputStream.

## References

[1] [Java SE 8 API Document](https://docs.oracle.com/javase/8/docs/api/)

[2] [Java File Descriptor Example](https://examples.javacodegeeks.com/core-java/io/filedescriptor/java-file-descriptor-example/)

[3] [How to get a path to a resource in a Java JAR file - Stack Overflow](https://stackoverflow.com/questions/941754/how-to-get-a-path-to-a-resource-in-a-java-jar-file)