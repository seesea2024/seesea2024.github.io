---
layout: post
title: java doulbe long读写是否原子性
categories: [java double long]
---
java double long占用8个字节，读写double 或者 long字段是否保持原子性呢

参考Java语言规范，对double和long的读写拆分为2次4字节的操作，不能保证原子性。但是声明为volatile后，jvm虚拟机一般会保证不拆分，保证原子性。具体参考下面Oracle官方文档。

```
17.7. Non-Atomic Treatment of double and long
For the purposes of the Java programming language memory model, a single write to a non-volatile long or double value is treated as two separate writes: one to each 32-bit half. This can result in a situation where a thread sees the first 32 bits of a 64-bit value from one write, and the second 32 bits from another write.

Writes and reads of volatile long and double values are always atomic.

Writes to and reads of references are always atomic, regardless of whether they are implemented as 32-bit or 64-bit values.

Some implementations may find it convenient to divide a single write action on a 64-bit long or double value into two write actions on adjacent 32-bit values. For efficiency's sake, this behavior is implementation-specific; an implementation of the Java Virtual Machine is free to perform writes to long and double values atomically or in two parts.

Implementations of the Java Virtual Machine are encouraged to avoid splitting 64-bit values where possible. Programmers are encouraged to declare shared 64-bit values as volatile or synchronize their programs correctly to avoid possible complications.
```

## 参考
[读写是否原子性](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.7)