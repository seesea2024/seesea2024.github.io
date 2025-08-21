---
published: true
date: 2017-08-27T08:32:31.000Z
categories: midware
layout: post
---
## 幂等性
f(x)=f(f(x))

### TCP
TCP协议能够保证幂等的核心在于sequence number字段，一个序列号的在较长的一段时间内均不会出现重复。

### 应用层
对于应用层的协议设计，原理和TCP是类似的，我们需要一个不重复的序列号。再简单一点说，在一个业务流程的处理中，我们需要一个不重复的业务流水号，以保证幂等性。比如DB表中的UK自动（业务id）

### RPC调用方／服务方
[TCC](https://github.com/liuyangming/ByteTCC/wiki)
