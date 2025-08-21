
---
layout: post
title: 修改boot classpath
categories: [JVM]
---
在boot classpath前面或者后面添加JAR

## 后面增加JAR
java -Xbootclasspath/a:"/path/to/my/folder/classes" Test1
-Xbootclasspath/a:path

Specify a colon-separated path of directires, JAR archives, and ZIP archives to append to the default bootstrap class path.

## 前面增加JAR
-Xbootclasspath/p:path

Specify a colon-separated path of directires, JAR archives, and ZIP archives to prepend in front of the default bootstrap class path. Note: Applications that use this option for the purpose of overriding a class in rt.jar should not be deployed as doing so would contravene the Java 2 Runtime Environment binary code license.