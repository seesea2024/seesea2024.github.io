---
layout: post
title: Java volatile
categories: [java]
---
Java volatile

## volatile 语义

1. 内存可见性，不保证原子性（++i）
2. 禁止指令重排 (单例设计模式)
3. 保证double/long的读写原子性（不然32bit+32bit操作会有不一致）

关于第三点，可以参考[jls](https://docs.oracle.com/javase/specs/jls/se7/html/jls-17.html#jls-17.7)

```
17.7. Non-atomic Treatment of double and long
For the purposes of the Java programming language memory model, a single write to a non-volatile long or double value is treated as two separate writes: one to each 32-bit half. This can result in a situation where a thread sees the first 32 bits of a 64-bit value from one write, and the second 32 bits from another write.

Writes and reads of volatile long and double values are always atomic.

Writes to and reads of references are always atomic, regardless of whether they are implemented as 32-bit or 64-bit values.

Some implementations may find it convenient to divide a single write action on a 64-bit long or double value into two write actions on adjacent 32-bit values. For efficiency's sake, this behavior is implementation-specific; an implementation of the Java Virtual Machine is free to perform writes to long and double values atomically or in two parts.

Implementations of the Java Virtual Machine are encouraged to avoid splitting 64-bit values where possible. Programmers are encouraged to declare shared 64-bit values as volatile or synchronize their programs correctly to avoid possible complications.
```