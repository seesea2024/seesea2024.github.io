# Java动态代理  

![动态生成字节码](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/java_dynamic_proxy.png?raw=true)


## ASM：
ASM 是一个底层的Java字节码生成工具。它能够以二进制形式修改已有类或者动态生成类。ASM 可以直接产生二进制 class 文件，也可以在类被加载入 Java 虚拟机之前动态改变类行为。ASM 从类文件中读入信息后，能够改变类行为，分析类信息，甚至能够根据用户要求生成新类。

不过ASM在创建class字节码的过程中，操纵的级别是底层JVM的汇编指令级别，这要求ASM使用者要对class组织结构和JVM汇编指令有一定的了解。

## Javassist
Javassist是一个开源的分析、编辑和创建Java字节码的类库。是由东京工业大学的数学和计算机科学系的 Shigeru Chiba （千叶滋）所创建的。它已加入了开放源代码JBoss 应用服务器项目,通过使用Javassist对字节码操作为JBoss实现动态AOP框架。javassist是jboss的一个子项目，其主要的优点，在于简单，而且快速。直接使用java编码的形式，而不需要了解虚拟机指令，就能动态改变类的结构，或者动态生成类。

## CGLIB（类继承）
CGLIB（Code Generation Library），是一个强大的，高性能，高质量的Code生成类库，它可以在运行期扩展Java类与实现Java接口(类继承)。

cglib 创建某个类A的动态代理类的模式是：
1. 查找A上的所有非final 的public类型的方法定义；
2. 将这些方法的定义转换成字节码；
3. 将组成的字节码转换成相应的代理的class对象；
4. 实现 MethodInterceptor接口，用来处理 对代理类上所有方法的请求（这个接口和JDK动态代理InvocationHandler的功能和角色是一样的）

## JDK的动态代理（接口）
InvocationHandler/Proxy

```java
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h)
    throws IllegalArgumentException {

    }
```

```java
public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

## 总结
JDK动态代理创建速度优于CGLIB动态代理，但是在函数的调用性能上远不如CGLIB和Javassist。故CGLIB和Javassist整体性能上比JDK动态代理好。Javassist字节码最快，CGLIB次之，是JDK的两倍。

另外**Aspectj**不能算动态代理，应该是静态代理，因为它采用的是编译器植入。用aspectj，需要写相应的xml，定义切面，织入点等，然后由aspectj的编译器来编译出新的字节码文件，这明显是静态代理。
