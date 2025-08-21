---
title: java如何通过反射获取方法参数名
date: 2017-08-26 21:32:31 Z
categories: jdk
layout: post
---
参考：http://blog.csdn.net/revitalizing/article/details/71036970

## Spring实现-通过spring的LocalVariableTableParameterNameDiscoverer

spring mvc 方法参数绑定请求路径参数

如果不用Class，而是通过spring注入的实例，然后instance.getClass.getDeclaredMethods()则无法得到参数名，调试时看到方法名称是通过jdk代理过的，
拿不到参数名

## 通过Java8的Parameter类

在Java 8之前的版本，代码编译为class文件后，方法参数的类型是固定的，但参数名称却丢失了，这和动态语言严重依赖参数名称形成了鲜明对比。
现在，java 8开始在class文件中保留参数名，给反射带来了极大的便利。jdk8增加了类Parameter

如果编译级别低于1.8，得到的参数名称是无意义的arg0、arg1…… 遗憾的是，保留参数名这一选项由编译开关javac -parameters打开，默认是关闭的。 
注意此功能必须把代码编译成1.8版本的class才行。
