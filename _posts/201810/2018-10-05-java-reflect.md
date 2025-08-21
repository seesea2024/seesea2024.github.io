---
layout: post
title: getMethods 和 getDeclaredMethods
categories: [java]
---
getMethods和getDeclaredMethods的区别

## getMethods

> public Method[] getMethods()
>                    throws SecurityException
> Returns an array containing Method objects reflecting all the public methods of the class or interface represented by this Class object, including those declared by the class or interface and those inherited from superclasses and superinterfaces.

## getDeclaredMethods

> public Method[] getDeclaredMethods()
>                            throws SecurityException
> Returns an array containing Method objects reflecting all the declared methods of the class or interface represented by this Class object, including public, protected, default (package) access, and private methods, but excluding inherited methods.

## 备注
下面反射方法类似。

getDeclaredFields 和 getFields
getDeclaredField 和 getField