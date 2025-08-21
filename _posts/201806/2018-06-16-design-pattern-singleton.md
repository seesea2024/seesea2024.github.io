---
layout: post
title: 设计模式 - 单例
categories: [设计模式]
---
# 单例实现方式

## 单例1

```java
public class DraconianSingleton {
    private static DraconianSingleton instance;
    public static synchronized DraconianSingleton getInstance() {
        if (instance == null) {
            instance = new DraconianSingleton();
        }
        return instance;
    }
 
    private DraconianSingleton() {

    }
}
```

## 单例2

* static volatile 的instance (layz init with double check)
* 多线程环境下，double check考虑 (synchronized class类，非实例)
* double check模式中volatile的原因是避免指令排序
    1. 分布对象的内存空间
    2. 初始化对象
    3. 设置instance对象指向内存空间

```java
public class DclSingleton {
    private static volatile DclSingleton instance;
    public static DclSingleton getInstance() {
        if (instance == null) {
            synchronized (DclSingleton.class) {
                if (instance == null) {
                    instance = new DclSingleton();
                }
            }
        }
        return instance;
    }
 
    // private constructor and other methods...
    private DraconianSingleton() {

    }
}
```

## 单例3

static final 的instance域 (eager init)

```java
public class EarlyInitSingleton {
    private static final EarlyInitSingleton INSTANCE = new EarlyInitSingleton();
    public static EarlyInitSingleton getInstance() {
        return INSTANCE;
    }
     
     // private constructor and other methods...
    private DraconianSingleton() {

    }
}
```

## 单例4

```java
public class InitOnDemandSingleton {
    private static class InstanceHolder {
        private static final InitOnDemandSingleton INSTANCE = new InitOnDemandSingleton();
    }
    public static InitOnDemandSingleton getInstance() {
        return InstanceHolder.INSTANCE;
    }
 
     // private constructor and other methods...
    private DraconianSingleton() {

    }
}
```

## 单例5

```java
public enum EnumSingleton {
    INSTANCE;
 
    // other methods...
}
```
## 注意事项
1. 私有的构造函数 
2. 单例类如果实现了Serializable接口（直接或者间接），需要添加一个readResolve()方法，用instance域替换掉Serialization机制自动生成的实例类对象

```java
private Object readResolve(){
       return instance;
}
```

## 参考

[baeldung](http://www.baeldung.com/java-singleton-double-checked-locking)
