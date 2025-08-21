---
title: Class Loader
categories: [iteye, ClassLoader]
layout: post
excerpt_separator: <!--more-->
---
Java ClassLoader<!--more-->

# 调用自定义ClassLoader

```java
public class ClassLoaderTest {
    public static void main(String[] args) {
        MyClassLoader cl1 = new MyClassLoader();

        try {
            Class c1 = cl1.loadClass("Hello");
            Object object = c1.newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
            System.out.println("main-ClassNotFoundException");
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        }
    }
}
```

```java
protected Class<?> loadClass(String name, boolean resolve)
  throws ClassNotFoundException {
     
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            long t0 = System.nanoTime();
                try {
                    if (parent != null) {
                        c = parent.loadClass(name, false);
                    } else {
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                    // ClassNotFoundException thrown if class not found
                    // from the non-null parent class loader
                }
 
                if (c == null) {
                    // If still not found, then invoke findClass in order
                    // to find the class.
                    c = findClass(name);
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
```

# 实现自定义java.lang.ClassLoader中的关键方法

```java
public class MyClassLoader extends ClassLoader {

    @Override
    public Class findClass(String name) {
        byte[] data = loadClassData(name);

        // 字节流转化为Class类
        return defineClass(data, 0, data.length);
    }

    public byte[] loadClassData(String name) {
        // 根据文件名读取class文件并转换未字节流
    }
}
```

### 类加载器的Delegate

除了引导类加载器之外，所有的类加载器都有一个父类加载器。类加载器在尝试自己去查找某个类的字节代码并定义它时，会先代理给其父类加载器，由父类加载器先去尝试加载这个类，依次类推。通过代理模式，对于 Java 核心库的类的加载工作由引导类加载器来统一完成，保证了Java 应用所使用的都是同一个版本的 Java 核心库的类，是互相兼容的。比如java.lang.Object  

### 类的定义加载器与初始加载器

真正完成类的加载工作的类加载器和启动这个加载过程的类加载器，有可能不是同一个。真正完成类的加载工作是通过调用 defineClass 来实现的；而启动类的加载过程是通过调用 loadClass 来实现的。前者称为一个类的定义加载器（defining loader），后者称为初始加载器（initiating loader）。在 Java 虚拟机判断两个类是否相同的时候，使用的是类的定义加载器。也就是说，哪个类加载器启动类的加载过程并不重要，重要的是最终定义这个类的加载器。两种类加载器的关联之处在于：一个类的定义加载器是它引用的其它类的初始加载器。如类 com.example.Outer 引用了类 com.example.Inner，则由类 com.example.Outer 的定义加载器负责启动类 com.example.Inner 的加载过程。

### 线程上下文类加载器  

```java
java.lang.Thread.getContextClassLoader();
java.lang.Thread.setContextClassLoader(ClassLoader cl); 
```

 如果没有通过 setContextClassLoader(ClassLoader cl) 方法进行设置的话，线程将继承其父线程的上下文类加载器。Java 应用运行的初始线程的上下文类加载器是系统类加载器。在线程中运行的代码可以通过此类加载器来加载类和资源。
 
 这通常发生在JVM核心代码必须动态加载由应用程序动态提供的资源时。拿JNDI为例，它的核心是由JRE核心类(rt.jar)实现的。但这些核心JNDI类必须能加载由第三方厂商提供的JNDI实现。这种情况下调用父类加载器（原初类加载器）来加载只有其子类加载器可见的类，这种代理机制就会失效。解决办法就是让核心JNDI类使用线程上下文类加载器，从而有效的打通类加载器层次结构，逆着代理机制的方向使用类加载器。常见的 SPI 有 JDBC、JCE、JNDI、JAXP 和 JBI 等。
 
 这些 SPI 的接口由 Java 核心库来提供。这些 SPI 的实现代码很可能是作为 Java 应用所依赖的 jar 包被包含进来，可以通过类路径（CLASSPATH）来找到，如实现了 JAXP SPI 的 Apache Xerces 所包含的 jar 包。线程上下文类加载器正好解决了这个问题。如果不做任何的设置，Java 应用的线程的上下文类加载器默认就是系统上下文类加载器。在 SPI 接口的代码中使用线程上下文类加载器，就可以成功的加载到 SPI 实现的类。线程上下文类加载器在很多 SPI 的实现中都会用到.

### 来自网络的类加载器 
NetworkClassLoader 负责通过网络下载 Java 类字节代码并定义出 Java 类。再通过NetworkClassLoader加载了某个版本的类之后，一般有两种做法来使用它。第一种做法是使用 Java 反射 API。另外一种做法是使用接口。需要注意的是，并不能直接在客户端代码中引用从服务器上下载的类，因为客户端代码的类加载器找不到这些类。使用 Java 反射 API 可以直接调用 Java 类的方法。而使用接口的做法则是把接口的类放在客户端中，从服务器上加载实现此接口的不同版本的类。在客户端通过相同的接口来使用这些实现类。  

### Web/Java EE 应用类加载器 delegate.  
以 Apache Tomcat 来说，每个 Web 应用都有一个对应的类加载器实例。该类加载器也使用代理模式，所不同的是它是首先尝试去加载某个类，如果找不到再代理给父类加载器。这与一般类加载器的顺序是相反的。这是 Java Servlet 规范中的推荐做法，其目的是使得 Web 应用自己的类的优先级高于 Web 容器提供的类。这种代理模式的一个例外是：Java 核心库的类是不在查找范围之内的。这也是为了保证 Java 核心库的类型安全。
