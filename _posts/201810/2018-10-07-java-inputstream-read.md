---
layout: post
title: 为什么InputStream read返回int
categories: [Java]
---
Java InputStream read返回int

## 问题
InputStream 操作的是字节流，read字面上理解应该返回字节，为什么api中返回的是int

```
public abstract int read()
                  throws IOException
Reads the next byte of data from the input stream. The value byte is returned as an int in the range 0 to 255. If no byte is available because the end of the stream has been reached, the value -1 is returned. This method blocks until input data is available, the end of the stream is detected, or an exception is thrown.
A subclass must provide an implementation of this method.

Returns:
the next byte of data, or -1 if the end of the stream is reached.
Throws:
IOException - if an I/O error occurs.
```

## 原因

* byte表示的范围是-128～127，不能表示0～255
* EOF（-1）？
* 为什么不用short的原因是int效率更高（字节对齐？），虽然内存占用会大一些

## 参考

[SOF](https://stackoverflow.com/questions/4659659/why-does-inputstreamread-return-an-int-and-not-a-byte)