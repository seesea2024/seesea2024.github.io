---
layout: post
title: show me the code - 一致性hash实现
categories: [一致性hash]
---
## show me the code - 一致性hash


### 介绍
libketama在memcached客户端一致性hash算法中的运用。

* TreeMap的使用
* 虚拟节点


### 代码

```java
import java.util.Collection;  
import java.util.SortedMap;  
import java.util.TreeMap;  

public class ConsistentHash<T> {  

 private final HashFunction hashFunction;  
 private final int numberOfReplicas;  
 private final SortedMap<Integer, T> circle = new TreeMap<Integer, T>();  

 public ConsistentHash(HashFunction hashFunction,  
   int numberOfReplicas, Collection<T> nodes) {  

   this.hashFunction = hashFunction;  
   this.numberOfReplicas = numberOfReplicas;  

   for (T node : nodes) {  
     add(node);  
   }  
 }  

 public void add(T node) {  
   for (int i = 0; i < numberOfReplicas; i++) {  
     circle.put(hashFunction.hash(node.toString() +":" + i),  
       node);  
   }  
 }  

 public void remove(T node) {  
   for (int i = 0; i < numberOfReplicas; i++) {  
     circle.remove(hashFunction.hash(node.toString() + ":" + i));  
   }  
 }  

 public T get(Object key) {  
   if (circle.isEmpty()) {  
     return null;  
   }  
   int hash = hashFunction.hash(key);  
   SortedMap<Integer, T> tailMap =  
       circle.tailMap(hash);  
    hash = tailMap.isEmpty() ?  
            circle.firstKey() : tailMap.firstKey();  
   return circle.get(hash);  
 }   

}
```

### 参考
[libketama](http://www.10tiao.com/html/546/201609/2650625974/1.html)