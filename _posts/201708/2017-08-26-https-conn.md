---
title: https
date: 2017-08-26 18:32:31 Z
categories: architect
layout: post
---

摘自：http://blog.csdn.net/wangjun5159/article/details/51510594

![https建立连接](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/https-establish-conn.jpg?raw=true)
*  客户端发送请求到服务器端
*  服务器端返回证书和公开密钥，公开密钥作为证书的一部分而存在
*  客户端验证证书和公开密钥的有效性，如果有效，则生成共享密钥并使用公开密钥加密发送到服务器端
*  服务器端使用私有密钥解密数据，并使用收到的共享密钥加密数据，发送到客户端
*  客户端使用共享密钥解密数据
*  SSL加密建立

支付宝涉及到金融，所以出于安全考虑采用https这个，可以理解，为什么百度、知乎等也采用这种方式？为了防止运营商劫持！http通信时，运营商在数据中插入各种广告，用户看
