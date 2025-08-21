---
layout: post
title: Java 对象大小
categories: [Java]
---
java对象内存layout

* Class : A pointer to the class information, which describes the object type. In the case of a java.lang.Integer object, for example, this is a pointer to the java.lang.Integer class.
* Flags : A collection of flags that describe the state of the object, including the hash code for the object if it has one, and the shape of the object (that is, whether or not the object is an array).
* Lock : The synchronization information for the object — that is, whether the object is currently synchronized.

![Java Process Memory](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/201810/java-process-memory-layout.gif?raw=true)

![32 Java Integer Object Memory](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/201810/Integer-object-32bit.gif?raw=true)

![Java Array](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/201810/java-array.gif?raw=true)

![Java String](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/201810/java-string.gif?raw=true)

[参考](https://www.ibm.com/developerworks/library/j-codetoheap/index.html)