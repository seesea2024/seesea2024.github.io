---
title: 数据库 replication and partition
categories: [iteye,数据库]
layout: post
excerpt_separator: <!--more-->
---
数据库replication和partition <!--more-->

## 分库/拆库
partition应用在划分正交的业务领域到不同的数据库

## 复制
replication同loader balance用在一起。有同步和异步两种方式。

* Synchoronus

网速要求比较快，用在业务关键的领域比如金融。不同节点相互备份和partitioning

* Asynchoronous

异步方式用Queue，Pub/Sub方式。应用场景包括disarster recovery,网速慢时
