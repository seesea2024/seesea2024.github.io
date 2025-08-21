---
title: java性能调优
date: 2017-02-03 23:19:04 Z
categories:
- blog
layout: post
---

### java heap dump

1. jps -ml 得到java进程号 pid
1. jmap -dump:format=b,file=heap.dump [pid] 或者 jmap -histo pid > dump.log
1. jhat heap.dump
1. 访问http://localhost:7000


### thread dump

1. ps -ef | grep 进程名 | grep -v grep
1. top -Hp pid得到线程id tid
1. printf "%x\n" tid得到线程id的十六进制表示如54ee
1. jstack pid | grep 54ee
1. 根据stack信息找到有问题的类和方法

### jstat

1. jstat -gc pid 250 4
* S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）
* EC、EU：Eden区容量和使用量
* OC、OU：年老代容量和使用量
* PC、PU：永久代容量和使用量
* YGC、YGT：年轻代GC次数和GC耗时
* FGC、FGCT：Full GC次数和Full GC耗时
* GCT：GC总耗时
1. jstat -gccapacity pid
1. jstat -gcutil pid
1. jstat -gcnew pid
1. jstat -gcnewcapacity pid
1. jstat -gcold pid
1. jstat -gcoldcapacity pid
1. jstat -gcpermcapacity pid
1. jstat -class pid
1. jstat -compiler pid
1. jstat -printcompilation pid
