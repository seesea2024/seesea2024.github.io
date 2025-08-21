---
layout: post
title: Java泛型
categories: [java]
---
Java generic（泛型）笔记

## extends 和 super

```java
 public static <T extends Comparable<? super T>> void sort(List<T> list) {
        list.sort(null);
    }
```

这里的泛型方法中的T要么它自己，要么它的父类需要 implements Comparable。理解起来是要么按照T的属性或者T的父类属性排序。

[SOF](https://stackoverflow.com/questions/25779184/explanation-of-generic-t-extends-comparable-super-t-in-collection-sort-com)

