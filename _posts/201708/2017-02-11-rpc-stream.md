---
title: 通过网络加载类
categories: [iteye, rpc]
layout: post
excerpt_separator: <!--more-->
---
RMI / RPC / Web Service 调用时需要客户端服务端维护同样的调用接口?<!--more-->

利用java的对象流传送对象，将服务器端的任务以对象的方式传送给客户端，客户端同样适用对象流接收任务，并复原对象的各个属性。 ObjectOutputStream和ObjectInputStream ObjectOutputStream在传送对象的时候要求对象是实现了序列化接口的，这要求在编写任务的时候需要实现此接口。 ObjectInputStream在接收对象的时候，需要能够找到对应的类定义，这需要客户端启动之后，能够动态的加载任务类，可以考虑自定义Classloader，在特定的目录中加载任务类定义。这个过程也是由服务器端发起，服务器端在发送任务对象前，先判断任务对象的类定义（字节码）是否已经发送给了客户端，如果没有，则先发送字节码定义给客户端。   我猜：客户端的接口是为了客户端自定义的ClassLoader加载网络过来的对象流后，能够Cast成给定的对象。