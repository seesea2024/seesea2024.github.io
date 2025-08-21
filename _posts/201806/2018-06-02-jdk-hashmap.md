---
layout: post
title: JDK HashMap
categories: [JDK HashMap]
---
JDK HashMap 知识要点

* 数组 + 链表 + 红黑树（JDK 8）
* JDK 7 并发扩容时CPU占用高问题
* capacity, default table length 16
* 链表长度超过8时转红黑树
* 扩容阀值threshold = table length * load factor，kv健值对个数超过阀值时开始扩容
* table数组位置定位

```
(h = k.hashCode()) ^ (h >>> 16) & (table.length -1)

在JDK1.8的实现中，优化了高位运算的算法，通过hashCode()的高16位异或低16位实现的：(h = k.hashCode()) ^ (h >>> 16)，主要是从速度、功效、质量来考虑的，这么做可以在数组table的length比较小的时候，也能保证考虑到高低Bit都参与到Hash的计算中，同时不会有太大的开销。
```

* JDK7扩容时链表中节点位置会反转，JDK8不会（死循环，cpu高的原因）