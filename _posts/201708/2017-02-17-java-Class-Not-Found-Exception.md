---
title: ClassNotFoundException and NoClassDefFoundError
categories: [iteye, java]
layout: post
excerpt_separator: <!--more-->
---
ClassNotFoundException and NoClassDefFoundError<!--more-->

From: http://jroller.com/sjivan/entry/difference_between_classnotfoundexception_and_noclassdeffounderror

------
   
A ClassNotFoundException is thrown when the reported class is not found by the ClassLoader. This typically means that the class is missing from the CLASSPATH. It could also mean that the class in question is trying to be loaded from another class which was loaded in a parent classloader and hence the class from the child classloader is not visible. This is sometimes the case when working in more complex environments like an App Server (WebSphere is infamous for such classloader issues).     

People often tend to confuse java.lang.NoClassDefFoundError with java.lang.ClassNotFoundException however there's an important distinction. For example an exception (an error really since java.lang.NoClassDefFoundError is a subclass of java.lang.Error) like 

```java
java.lang.NoClassDefFoundError:
org/apache/activemq/ActiveMQConnectionFactory
```
 
 does not mean that the ActiveMQConnectionFactory class is not in the CLASSPATH. In fact its quite the opposite. It means that the class ActiveMQConnectionFactory was found by the ClassLoader however when trying to load the class, it ran into an error reading the class definition. This typically happens when the class in question has static blocks or members which use a Class that's not found by the ClassLoader. So to find the culprit, view the source of the class in question (ActiveMQConnectionFactory in this case) and look for code using static blocks or static members.   On examining the code, say you find a line of code like below, make sure that the class *SomeClass* in in your CLASSPATH.   
    
```java    
    private static SomeClass foo = new SomeClass();
```

注： 这种情况主要是编译时是有SomeClass的，运行时被删除找不到了就会报这种错误。 
