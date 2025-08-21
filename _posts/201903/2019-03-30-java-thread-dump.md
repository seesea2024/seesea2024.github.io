---
layout: post
title: Java thread Dump
categories: [JVM]
---
java thread dump分析

## 产生方式

jstack -l [pid]

kill -3 [pid]

// When the specified process is running on a 64-bit Java Virtual Machine, you might need to specify the -J-d64 option
jstack -J-d64 [core dump文件]

## SMR线程

This section contains the thread list Safe Memory Reclamation (SMR) information1, which enumerates the addresses of all non-JVM internal threads (e.g. non-VM and non-Garbage Collection (GC)). 

```
Threads class SMR info:
_java_thread_list=0x00000250e5488a00, length=13, elements={
0x00000250e4979000, 0x00000250e4982800, 0x00000250e52f2800, 0x00000250e4992800,
0x00000250e4995800, 0x00000250e49a5800, 0x00000250e49ae800, 0x00000250e5324000,
0x00000250e54cd800, 0x00000250e54cf000, 0x00000250e54d1800, 0x00000250e54d2000,
0x00000250e54d0800
}

"Reference Handler" #2 daemon prio=10 os_prio=2 tid=0x00000250e4979000 nid=0x3c28 waiting on condition  [0x000000b82a9ff000]

"Finalizer" #3 daemon prio=8 os_prio=1 tid=0x00000250e4982800 nid=0x2a54 in Object.wait()  [0x000000b82aaff000]

"Signal Dispatcher" #4 daemon prio=9 os_prio=2 tid=0x00000250e52f2800 nid=0x2184 runnable  [0x0000000000000000]

"Attach Listener" #5 daemon prio=5 os_prio=2 tid=0x00000250e4992800 nid=0x1624 waiting on condition  [0x0000000000000000]


"C2 CompilerThread0" #6 daemon prio=9 os_prio=2 tid=0x00000250e4995800 nid=0x4198 waiting on condition  [0x0000000000000000]

"C2 CompilerThread1" #7 daemon prio=9 os_prio=2 tid=0x00000250e49a5800 nid=0x3b98 waiting on condition  [0x0000000000000000]

"C1 CompilerThread2" #8 daemon prio=9 os_prio=2 tid=0x00000250e49ae800 nid=0x1a84 waiting on condition  [0x0000000000000000]

"Sweeper thread" #9 daemon prio=9 os_prio=2 tid=0x00000250e5324000 nid=0x5f0 runnable  [0x0000000000000000]

"Service Thread" #10 daemon prio=9 os_prio=0 tid=0x00000250e54cd800 nid=0x169c runnable  [0x0000000000000000]


"Common-Cleaner" #11 daemon prio=8 os_prio=1 tid=0x00000250e54cf000 nid=0x1610 in Object.wait()  [0x000000b82b2fe000]

"Thread-0" #12 prio=5 os_prio=0 tid=0x00000250e54d1800 nid=0xdec waiting for monitor entry  [0x000000b82b4ff000]

"Thread-1" #13 prio=5 os_prio=0 tid=0x00000250e54d2000 nid=0x415c waiting for monitor entry  [0x000000b82b5ff000]

"DestroyJavaVM" #14 prio=5 os_prio=0 tid=0x00000250e54d0800 nid=0x2b8c waiting on condition  [0x0000000000000000]

```

## VM和GC线程

```
"VM Thread" os_prio=2 tid=0x00000250e496d800 nid=0x1920 runnable  
"GC Thread#0" os_prio=2 tid=0x00000250c35b5800 nid=0x310c runnable  
"GC Thread#1" os_prio=2 tid=0x00000250c35b8000 nid=0x12b4 runnable  
"GC Thread#2" os_prio=2 tid=0x00000250c35ba800 nid=0x43f8 runnable  
"GC Thread#3" os_prio=2 tid=0x00000250c35c0800 nid=0x20c0 runnable  
"G1 Main Marker" os_prio=2 tid=0x00000250c3633000 nid=0x4068 runnable  
"G1 Conc#0" os_prio=2 tid=0x00000250c3636000 nid=0x3e28 runnable  
"G1 Refine#0" os_prio=2 tid=0x00000250c367e000 nid=0x3c0c runnable  
"G1 Refine#1" os_prio=2 tid=0x00000250e47fb800 nid=0x3890 runnable  
"G1 Refine#2" os_prio=2 tid=0x00000250e47fc000 nid=0x32a8 runnable  
"G1 Refine#3" os_prio=2 tid=0x00000250e47fd800 nid=0x3d00 runnable  
"G1 Young RemSet Sampling" os_prio=2 tid=0x00000250e4800800 nid=0xef4 runnable  
"VM Periodic Task Thread" os_prio=2 tid=0x00000250e54d6800 nid=0x3468 waiting on condition  


```
