---
layout: post
title: private access level
categories: [java]
---
java中，除了类内部，什么情况下可以访问private变量？

```java
package com.stone.javacore;

public class PrivateAccess {
    public static class Line{
        private double k;
        private double b;
        private boolean isVertical;

        public Line(double k, double b, boolean isVertical){
            this.k = k;
            this.b = b;
            this.isVertical = isVertical;
        }

    }

    public static  void main (String[] args){
        System.out.println(new Line(1, 2, false).k);  // 1.0
    }
}
```

原因[JLS chapter on accessibility](https://docs.oracle.com/javase/specs/jls/se7/html/jls-6.html#jls-6.6.1)

>Otherwise, if the member or constructor is declared private, then access is permitted if and only if it occurs within the body of the top level class (§7.6) that encloses the declaration of the member or constructor.