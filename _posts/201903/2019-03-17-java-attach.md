---
layout: post
title: Java Agent
categories: [JVM Instrument]
---
Java Agent机制

## 两个线程

- Signal Dispatcher(随jvm启动而启动,处理信号)
- Attach Listener, 这个线程通过Unix Domain Socket跟外部进程通讯（.java_pid<pid>）

> On Linux and Solaris, the client creates a file named .attach_pid<pid> and sends a SIGQUIT to the target JVM process. The existence of this file causes the SIGQUIT handler in HotSpot to start the attach listener thread.

* 注意 kill -3 并不会杀死java进程。默认行为是产生thread dump。除非JVM中重新定义了SIGQUIT(-3)的处理handler。[产生JVM thread dump](https://access.redhat.com/solutions/18178)

```shell
kill -3 pid
```

## 两个文件

- .attach_pid<pid> （启动attach listener线程用）
- .java_pid<pid> （Unix socket domain）


## 参考

[LinuxVirtualMachine](https://github.com/frohoff/jdk8u-dev-jdk/blob/master/src/solaris/classes/sun/tools/attach/LinuxVirtualMachine.java)
[你假笨@JVM](http://lovestblog.cn/blog/2014/06/18/jvm-attach/)
[非java进程attach JVM](https://github.com/aibaars/openjdk9-dev-hotspot/blob/8d19be0937c6212771e7cb536a707e690fe6b3cd/src/os/aix/vm/attachListener_aix.cpp)