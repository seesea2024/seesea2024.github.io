---
layout: post
title: Java 参数名自动发现
categories: [java]
---
如何动态获取java参数名

## java 8
Java 8中可以在编译时添加参数`javac -parameters`，再通过reflection获取

```java
public class Boundary {

    public void hello(String name, int age) {

    }

     public static void main(String[] args) {
        Method[] methods = Boundary.class.getMethods();
        for (Method method : methods) {
            System.out.print(method.getName() + "(");
            Parameter[] parameters = method.getParameters();
            for (Parameter parameter : parameters) {
                System.out.print(parameter.getType().getName() + " " + parameter.getName() + " ");
            }
            System.out.println(")");
        }
    }
}
```

编译时加了参数`javac -parameters` 时输出如下：

```
hello(java.lang.String name int age )
```

没有加参数`-parameters` 时输出结果如下：

```
hello(java.lang.String arg0 int arg1 )
```

## ASM + 编译时加上调试信息   
Java8以前编译时加上debug info via `javac -g` option

没有加-g选项时只有源文件和行号调试信息；加了-g选项后会包括本地变量调试信息（LocalVariableTable），比如变量名。

再通过Spring `LocalVariableTableParameterNameDiscoverer`获取

* source: Source file debugging information.
* lines: Line number debugging information.
* vars: Local variable debugging information.

## 使用场景

* @PathVariable
* @RequestParam
* @RequestHeade