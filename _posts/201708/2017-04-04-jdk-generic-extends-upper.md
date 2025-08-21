---
title: java 泛型 - 上界，下界
date: 2017-04-04 07:32:31 Z
categories:
- jdk
layout: post
---

少有的讲得比较清楚的 :(

泛型通配符<? extends T>来接收返回的数据，此写法的泛型集合不能使用 add 方法，而<? super T>不能使用 get 方法，做为接口调用赋值时易出错。

说明：扩展说一下 **PECS**(Producer Extends Consumer Super)原则：

1. 频繁往外读取内容的，适合用上界 Extends。
2. 经常往里插入的，适合用下界 Super。

几个具体的例子（注：java.util.java是java.sql.Timestamp的父类）

```java
# 上界用extends关键字声明，表示参数化的类型可能是所指定的类型，或者是此类型的子类
public void upperBound(List<? extends Date> list, Date date) {    
    Date now = list.get(0);    
    System.out.println("now==>" + now);    
    //list.add(date); //这句话无法编译    
    list.add(null);//这句可以编译，因为null没有类型信息    
}

public void testUpperBound() {    
    List<Timestamp> list = new ArrayList<Timestamp>();    
    Date date = new Date();    
    upperBound(list,date);    
}

# 泛型方法解决方法上面add问题
public <T extends Date> void upperBound2(List<T> list, T date) {    
    list.add(date);    
}  

public void testUpperBound2() {    
    List<Timestamp> list = new ArrayList<Timestamp>();    
    Date date = new Date();    
    Timestamp time = new Timestamp(date.getTime());    
    upperBound2(list,time);    
    //upperBound2(list,date);//这句同样无法编译    
}  

# 下界用super进行声明，表示参数化的类型可能是所指定的类型，或者是此类型的父类型，直至Object
public void lowerBound(List<? super Timestamp> list) {    
    Timestamp now = new Timestamp(System.currentTimeMillis());    
    list.add(now); 
    //下面语句不能编译，类型察除后变为：Timestamp time = (Timestamp)list.get(0);   
    //Timestamp time = list.get(0);  
}  

public void testLowerBound() {    
    List<Date> list = new ArrayList<Date>();    
    list.add(new Date());    
    lowerBound(list);    
}
```


### 参考
[阿里java规范](https://yq.aliyun.com/attachment/download/?spm=5176.100239.blogcont69316.24.Iilb2k&id=1200)

<http://fyting.iteye.com/blog/122732>




