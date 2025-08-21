---
title: "架构 - 非功能性需求"
categories: [iteye, 架构需求]
layout: post
excerpt_separator: <!--more-->
---
架构设计中考量的是Non Functional factors <!--more-->


### Response Time     
处理一次请求的时间或者平均时间   

### Throughput     
一般以 hits per second or transactions per second 度量   

### Scalability     
Scale up(Vertical Scalability) and Scale out ( horizontal scalability )    

Vertical Scalability我的理解是提高服务器硬件配置比如CPU和内存，同时也包括在一台物理机上部署多个Server。而Horizontal Scalability我的理解是集群之类的方案。集群环境下并不会随着集群中Node的增加Throughput也会线性的增加。因为集群也会引入Cluster node 的management Overhead，并且随着集群中Node节点的增加，必然让后端的database或者EIS成为瓶颈。有时候Performance和Scalability是有冲突的，比如Cluster环境下Session复制问题。如果不考虑Scalability，单机上存储session性能会不错。如果考虑到扩展性采用集群，把session放到数据库中，程序的扩展性提高了，但数据库访问必然影响了性能。
