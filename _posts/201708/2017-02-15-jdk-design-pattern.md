---
title: JDK中用到的设计模式
categories: [iteye, 设计模式]
layout: post
excerpt_separator: <!--more-->
---
JDK中用到的设计模式<!--more-->

### java.io 中用到了装饰模式和适配器模式

* 装饰模式， In general, each read request made of a Reader causes a corresponding read request to be made of the underlying character or byte stream. It is therefore advisable to wrap a BufferedReader around any Reader whose read() operations may be costly, such as FileReaders and InputStreamReaders.   

```java
    BufferedReader in = new BufferedReader(new FileReader("foo.in"));
```

* 适配器模式, An InputStreamReader is a bridge from byte streams to character streams: It reads bytes and decodes them into characters using a specified charset. 

```java
    InputStreamReader(InputStream in, String charsetName)           
```

Create an InputStreamReader that uses the default charset.   

### 观察者模式 
Observer和Observable Listener   

### 工厂模式 
Service Provider

### 模板方法
* Integer.valueOf
* Class.forName

### 责任链模式
filter
