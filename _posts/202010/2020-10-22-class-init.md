---
layout: post
title: java类什么时候初始化
categories: [JVM]
---
这里的初始化指的是JVM启动时经过加载-链接（验证、准备、解析）后的初始化阶段

## 具体哪些操作会触发类的初始化呢

1. 当虚拟机启动时，初始化用户指定的主类；
2. 当遇到用以新建目标类实例的 new 指令时，初始化 new 指令的目标类；
3. 当遇到调用静态方法的指令时，初始化该静态方法所在的类；
4. 当遇到访问静态字段的指令时，初始化该静态字段所在的类；
5. 子类的初始化会触发父类的初始化；
6. 如果一个接口定义了 default 方法，那么直接实现或者间接实现该接口的类的初始化，会触发该接口的初始化；
7. 使用反射 API 对某个类进行反射调用时，初始化这个类；
8. 当初次调用 MethodHandle 实例时，初始化该 MethodHandle 指向的方法所在的类


* 下面单例类的实现就是利用了上面的第四个规则 `当遇到访问静态字段的指令时，初始化该静态字段所在的类`

```java
public class Singleton {
  private Singleton() {}
  private static class LazyHolder {
    static final Singleton INSTANCE = new Singleton();
  }
  public static Singleton getInstance() {
    return LazyHolder.INSTANCE;
  }
}
```