---
title: "Java堆内存参数调优 -Xms -Xmx 参数"
categories: [iteye, xms, xmx]
layout: post
---
Java堆内存参数调优

1. 有的性能调优文章中建议-Xms和-Xmx参数调整到相同值是考虑到物理Server上主要只有一个Java应用，没必要初始堆比较小慢慢调整堆大小。   相反不适合的情况是如果一台物理Server上安装有多个Java 应用的时候，JVM的这两个参数应该不同，这个各个JVM动态调整各自堆的大小，物理机内存得到最大的利用。  

2. -Xms -Xmx 参数大小决定了GC时间间隔和Pause Time.堆大小偏小的话，GC间隔时间小，Pause Time也小;堆大小偏大的话，GC间隔时间大，Pause Time也大。具体情况要多尝试找到平衡点。
