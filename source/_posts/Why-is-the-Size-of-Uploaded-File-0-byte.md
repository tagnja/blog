---
title: [error] Why is the Size of Uploaded File 0 byte
date: 2021-06-30 13:08:03
tags: error
categories:
- 其它
- 问题解决
---

## Background

I call APIs of OSS (object storage services) to upload my local file with Java Input Stream.

## Error Info

The uploaded file is 0 bytes.

## Solutions

- Check your accessed file exists and the file is not 0 byte.
- Before the inputStream upload, using Java InputStream available() method to check out whether the remaining number of bytes that can be read from the file input stream is 0.
- Make sure the remaining number of bytes that can be read from the file input stream is right.

## Reasons

The reason of the size of my uploaded file is 0 byte is I read the input stream two times. 

Most of input stream classes in Java API are not support read more than once. Because some Input Stream classes of Java API not support mark() and reset() method.

Although the `FilterInputStream` child classes can support read more than once by its `mark()`, `reset()` methods, and internal cache array `byte buf[]`, you still need to call it manually. The reset() method is for repositions this stream to the position at the time the `mark` method was last called on this input stream.
