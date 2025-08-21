---
layout: post
title: java对象内存对齐
categories: [java object layout]
---
如何通过JOL查看Java对象内存结构

```shell
curl -L -O curl -L -O https://repo.maven.apache.org/maven2/org/openjdk/jol/jol-cli/0.9/jol-cli-0.9-full.jar
java -cp jol-cli-0.9-full.jar org.openjdk.jol.Main internals java.lang.String
java -cp jol-cli-0.9-full.jar org.openjdk.jol.Main internals java.lang.Integer
```