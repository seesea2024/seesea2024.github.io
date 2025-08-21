---
layout: post
title: Java Core Dump
categories: [JVM]
---
Java Core Dump分析

Java程序运行时，有时会产生hs_err_pid<pid>.log及core Dump文件。发生core dump后，Java进程有时可以继续运行，但有时会挂掉。core dump文件保留了Java应用发生致命错误前的运行状态

## 如何生成core dump文件

```
echo /var/tmp/cores/%e-%p-%t.core > /proc/sys/kernel/core_pattern
```

```
-XX:ErrorFile=./hs_err_pid<pid>.log
```

## 生成的文件

### 二进制文件 java-[pid]-[unix时间].core

获取文件位置
```
$ cat /proc/sys/kernel/core_pattern
/var/tmp/cores/%e-%p-%u.core
$ ls /var/tmp/cores
java-21178-146385.core
```

```
# 
# A fatal error has been detected by the Java Runtime Environment: 
# 
# SIGBUS (0x7) at pc=0x00007fde40016a32, pid=7808, tid=0x00007fde4075b700 
# 
# JRE version: Java(TM) SE Runtime Environment (8.0_92-b14) (build 1.8.0_92-b14) 
# Java VM: Java HotSpot(TM) 64-Bit Server VM (25.92-b14 mixed mode linux-amd64 compressed oops) 
# Problematic frame: 
# C [libzip.so+0x11a32] newEntry+0x62 
# 
# Failed to write core dump. Core dumps have been disabled. To enable core dumping, try "ulimit -c unlimited" before starting Java again 
# 
# If you would like to submit a bug report, please visit: 
# http://bugreport.java.com/bugreport/crash.jsp 
# The crash happened outside the Java Virtual Machine in native code. 
# See problematic frame for where to report the bug. 
# 
```

### hs_err_pid[pid].log

- java应用崩溃日志中可以看到hs_err-pid[pid].log文件位置
```
#
# A fatal error has been detected by the Java Runtime Environment:
#
# SIGSEGV (0xb) at pc=0x00000000000010c6, pid=2446, tid=140570273433344
#
# JRE version: Java(TM) SE Runtime Environment (8.0_66-b17) (build 1.8.0_66-b17)
# Java VM: Java HotSpot(TM) 64-Bit Server VM (25.66-b17 mixed mode linux-amd64 compressed oops)
# Problematic frame:
# C 0x00000000000010c6
#
# Core dump written. Default location: /home/ewallet/quhua/quhua-online/default/tomcat/core or core.2446
#
# An error report file with more information is saved as:
# /home/default/tomcat/hs_err_pid2446.log
#
# If you would like to submit a bug report, please visit:
# http://bugreport.java.com/bugreport/crash.jsp
#
```

- 文本文件 hs_err_pid[pid].log

```
#
# A fatal error has been detected by the Java Runtime Environment:
#
#  SIGSEGV (0xb) at pc=0x0000003797807a91, pid=29071, tid=139901421901568
#
# JRE version: Java(TM) SE Runtime Environment (8.0_45-b14) (build 1.8.0_45-b14)
# Java VM: Java HotSpot(TM) 64-Bit Server VM (25.45-b02 mixed mode linux-amd64 compressed oops)
# Problematic frame:
# C  [libresolv.so.2+0x7a91]  __libc_res_nquery+0x1c1
#
# Failed to write core dump. Core dumps have been disabled. To enable core dumping, try "ulimit -c unlimited" before starting Java again
#
# If you would like to submit a bug report, please visit:
#   http://bugreport.java.com/bugreport/crash.jsp
# The crash happened outside the Java Virtual Machine in native code.
# See problematic frame for where to report the bug.
#

---------------  T H R E A D  ---------------

Current thread (0x0000000001e94800):  JavaThread "pool-1-thread-2" [_thread_in_native, id=30111, stack(0x00007f3d567e5000,0x00007f3d568e6000)]

siginfo: si_signo: 11 (SIGSEGV), si_code: 1 (SEGV_MAPERR), si_addr: 0x0000000000000003

Registers:
RAX=0x0000000000000000, RBX=0x0000000000000000, RCX=0x0000000000000000, RDX=0x0000000000000050
RSP=0x00007f3d568e2280, RBP=0x00007f3d568e2570, RSI=0x0000000000000000, RDI=0x00000000ffffffff
R8 =0x0000000000000000, R9 =0x0000000000000000, R10=0x000000000007a337, R11=0x0000000000000213
R12=0x00007f3d568e2ef0, R13=0x00007f3d568e22b0, R14=0x0000000000000000, R15=0x00007f3d568e5db8
RIP=0x0000003797807a91, EFLAGS=0x0000000000010246, CSGSFS=0x0000000000000033, ERR=0x0000000000000004
  TRAPNO=0x000000000000000e

Top of Stack: (sp=0x00007f3d568e2280)
0x00007f3d568e2280:   b8e4bfb900000800 00007f3d568e3760
0x00007f3d568e2290:   00007f3d568e3758 00007f3d568e377c
0x00007f3d568e22a0:   00007f3d568e3778 6f6e6b6e56a88a58
0x00007f3d568e22b0:   00000100000149a0 7a68710800000000
0x00007f3d568e22c0:   6970067363642d78 6d6f63036e61676e
....省略

Instructions: (pc=0x0000003797807a91)
0x0000003797807a71:   48 89 45 b8 48 8b 4d b8 0f b6 51 03 89 d3 83 e3
0x0000003797807a81:   0f 75 0d 0f b7 49 06 66 c1 c9 08 66 85 c9 75 4f
0x0000003797807a91:   0f b6 48 03 bf 0f 00 00 00 40 20 cf 75 0d 0f b7
0x0000003797807aa1:   70 06 66 c1 ce 08 66 85 f6 75 34 83 e1 0f 83 e2 

Register to memory mapping:

RAX=0x0000000000000000 is an unknown value
RBX=0x0000000000000000 is an unknown value
RCX=0x0000000000000000 is an unknown value
RDX=0x0000000000000050 is an unknown value
RSP=0x00007f3d568e2280 is pointing into the stack for thread: 0x0000000001e94800
RBP=0x00007f3d568e2570 is pointing into the stack for thread: 0x0000000001e94800
... 省略

```

## 如何分析

- 从hs-err-pid[pid].log文件中获取core dump线程id

- gdb

```
(gdb) set solib-search-path $JAVA_HOME/jre/bin/
(gdb) file $JAVA_HOME/jre/bin/java
(gdb) core java-230270-1551171676.core
(gdb) bt
or 
(gdb) where
```

- jstack 找到问题线程堆栈

```shell
jstack -J-d64 $JAVA_HOME/bin/java /var/tmp/cores/java-[pid]-[unix time].core
```

- jmap

```shell
jmap $JAVA_HOME/bin/java /var/tmp/cores/java-[pid]-[unix time].core
```