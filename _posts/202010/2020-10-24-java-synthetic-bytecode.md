---
layout: post
title: java中生成的bridge和syntheic代码
categories: [java bytecode synthetic]
---
Java中有些方法从字节码来看flags中包含ACC_BRIDGE, ACC_SYNTHETIC，一般是编译为class文件时生成的。这里看看java中有哪些场景会生成

## 如何查看字节码
* Class名包括/不包括.class扩展名都可以
```shell
javap -v -c package.Class名
```

## 继承override

父类
```java
public class Merchant {
    public Number actionPrice(double price) {
        return price * 0.8;
    }
}
```

子类
```java
public class NaiveMerchant extends Merchant {
    @Override
    public Double actionPrice(double price) {
        return 0.9 * price;
    }
}
```

注意子类覆盖父类的时候，返回值类型不同(父类是Number，子类是Double)。JVM虚拟机字节码层面跟JDK语言层面不一样，方法描述符包括返回类型，如果类型不相同，虚拟机怎么决定这是一个有效的继承覆盖呢？查看字节码会发现字节码中多了一个bridge桥接方法（flags: ACC_PUBLIC, ACC_BRIDGE, ACC_SYNTHETIC）,桥接方法会内部调用返回类型不同的真正方法。

```shell
javap -v -c  production.classes.com.stone.bytecode.NaiveMerchant
```

```
  public java.lang.Double actionPrice(double);
    descriptor: (D)Ljava/lang/Double;
    flags: ACC_PUBLIC
    Code:
      stack=4, locals=3, args_size=2
         0: ldc2_w        #2                  // double 0.9d
         3: dload_1
         4: dmul
         5: invokestatic  #4                  // Method java/lang/Double.valueOf:(D)Ljava/lang/Double;
         8: areturn
      LineNumberTable:
        line 12: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  this   Lcom/stone/bytecode/NaiveMerchant;
            0       9     1 price   D


  public java.lang.Number actionPrice(double);
    descriptor: (D)Ljava/lang/Number;
    flags: ACC_PUBLIC, ACC_BRIDGE, ACC_SYNTHETIC
    Code:
      stack=3, locals=3, args_size=2
         0: aload_0
         1: dload_1
         2: invokevirtual #12                 // Method actionPrice:(D)Ljava/lang/Double;
         5: areturn
      LineNumberTable:
        line 9: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       6     0  this   Lcom/stone/bytecode/NaiveMerchant;
}
SourceFile: "NaiveMerchant.java"
```

## 泛型类、泛型方法中的继承

```java
public interface Customer {

    double rate();

}
```

```java
public class VipCustomer implements Customer {
    @Override
    public double rate() {
        return 0.8;
    }
}
```

```java
abstract public class Shop<T extends Customer> {
    abstract double purchase(double price, T customer);
}
```
public class VipShop extends Shop<VipCustomer> {
    @Override
    double purchase(double price, VipCustomer customer) {
        return price * customer.rate();
    }
}
```java

```

```shell
javap -v -c  production.classes.com.stone.bytecode.VipShop
```

生成了下面的桥接方法, 注意因为泛型类型擦除，`T extends Customer`泛型类中的声明直接用`com.stone.bytecode.Customer`来代替了。
```
double purchase(double, com.stone.bytecode.Customer);
    descriptor: (DLcom/stone/bytecode/Customer;)D
    flags: ACC_BRIDGE, ACC_SYNTHETIC
    ...
    6: invokevirtual #4                  // Method purchase:(DLcom/stone/bytecode/VipCustomer;)D
    ...
```

可以看到桥接方法内部调用子类真正的purchase方法

