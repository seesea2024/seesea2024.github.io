---
layout: post
title: Java Agent对系统性能的影响有多大
categories: [JVM]
---
Java Agent对原有Java应用程序性能影响有多大？

## 背景
现有基于Java Agent的产品比如APM(Application Performance Management)、诊断工具、跟踪工具、测试平台一般声称他们产品对系统性能影响大概是X%。事实上，Java Agent对原始Java应用程序的影响真的可以用一个简单的数字表示么？网上有篇文章[Lies, damn lies and “our performance overhead is 2%”](https://plumbr.io/blog/java/lies-damn-lies-and-our-performance-overhead-is-2)分析的比较有意思，下文主要是参考原始作者的文章进行阐述。

## 系统资源损耗
业务应用避免系统资源耗尽。引起这种问题的原因可能是业务应用本身，可能是Java Agent，也可能是同台主机上的其他进程。
- CPU
  需要加入限流（throttling）机制，或者计算放到另外的主机服务器上进行，同注入的Java进程隔离。
- 内存
  堆内内存+堆外内存（本地内存）损耗，甚至可能引起OOM Killer直接杀掉内存占用大的进程。
- 磁盘空间和磁盘I/O
  避免日志文件把磁盘空间撑爆了
- 网络I/O
  对应采集数据并上报的Java agent需要避免过多占用网络带宽。考虑高效的数据结构或者压缩数据或者采样等。
  
## 常见问题

- GC
  Java Agent逻辑中new了堆对象。这些对象会增加GC的频率和STW的时间，影响系统吞掉量和响应时间。比如java agent修改的是一个Http请求的入口类，并获取Http header已经请求参数，如果按照流行的[greys](https://github.com/oldmanpushcart/greys-anatomy)的SPY方式，是会创建很多的对象。

- 破坏JIT优化
  Java Agent动态改名运行时Java应用的字节码会破坏JIT优化。简单的例子比如增加方法的大小，会影响内联（inlining）优化。另外一个是逃逸分析（Escape Analysis）。简单来说，就是堆对象栈上分配。如果java agent中用到了堆上对象就不会进行栈上分配的优化了。

- 更多
  

## 结论

不要相信产品提供的通用数字。在自己的环境，打开和关闭Java Agent，测试对比系统性能比如吞吐量/延迟/TPS等。


