---
layout: post
title: Java类型检查
categories: [Java]
---
Java类型转换前一般有三种方式进行类型检查

- isAssignableFrom
- isInstance
- instanceof

## isAssignableFrom
X.class.isAssignableFrom(Y.class)

> If X and Y are the same class, or X is Y's super class or super interface, return true, otherwise, false

## isInstance
X.class.isInstance(y)

> Say y is an instance of class Y, if X and Y are the same class, or X is Y's super class or super interface, return true, otherwise, false.

## instanceof
x instanceof X

> For instanceof you need to know the exact class X at compile time.