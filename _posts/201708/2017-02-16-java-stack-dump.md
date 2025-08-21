---
title: 如何简单的得到Java进程的stack trace dump
categories: [iteye, java]
layout: post
excerpt_separator: <!--more-->
---
如何简单的得到Java进程的stack trace dump<!--more-->

最简单直接变态的方法是：找到这个java进程的id（linux下的ps或者windows下的jps），直接kill掉。

```
kill -3 pid
```

java虚拟机没有hang住的情况下会立即dump stack trace；否则需要尝试下面的shell命令

```
kill pid
```

```
kill -9 pid
```

三者分别对应linux中信号SIGQUIT, SIGTERM, SIGKILL
