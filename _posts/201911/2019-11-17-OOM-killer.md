---
layout: post
title: OOM Killer
categories: [操作系统]
---
进程为什么无故者被杀掉或重启？

## 背景
调查问题过程中有时发现自己的进程无缘故被杀掉了或者多次重启（配置supervisord自动拉起）。这种情况可以查看是否是操作系统OOM killer干的。一般可以查看系统日志文件/var/log/messages中是否有这样的信息

```
Out of Memory: Kill process 1865（java）
```

## 分析
内核会通过特定的算法给每个进程计算一个分数来决定杀哪个进程，每个进程的oom分数可以从/proc/PID/oom_score中看到。

进程运行时有一个参数oom_adj可以调整oom_score。oom_adj的可调值为15到-16，其中15最大-16最小，-17为禁止使用OOM。oom_score为2的n次方计算出来的，其中n就是进程的oom_adj值，所以oom_score的分数越高就越会被内核优先杀掉。

进程避免OOM Kill 还是要看程序内存处理是否合理。