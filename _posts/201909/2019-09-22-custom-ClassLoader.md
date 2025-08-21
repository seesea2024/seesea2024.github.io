---
layout: post
title: 自定义ClassLoader热加载jar文件中的类
categories: [JDK]
---
本文主要分析和解决Spring Boot中自定义URLClassLoader热加载jar时遇到的坑。
模拟场景是动态修改一个class文件后，访问web服务endpoint后立即反应出来。每次修改后的class文件打包为一个jar文件并通过我们自定义的ClassLoader加载。

## 自定义URLClassLoader加载jar

- 自定义ClassLoader

```java
package com.stone.jdk.demohotswap.classloader;

import java.io.IOException;
import java.net.JarURLConnection;
import java.net.URL;
import java.net.URLClassLoader;
import java.net.URLConnection;
import java.util.ArrayList;
import java.util.List;

/**
 * 参考：https://blog.csdn.net/jianggujin/article/details/80536019
 */
public class MyClassLoader extends URLClassLoader {
    private static boolean canCloseJar;
    private List<JarURLConnection> cachedJarFiles = new ArrayList<>();

    static {
        // 1.7之后可以直接调用close方法关闭打开的jar，
        // 需要判断当前运行的环境是否支持close方法，
        // 如果不支持，需要缓存，避免卸载模块后无法删除jar
        try {
            URLClassLoader.class.getMethod("close");
            canCloseJar = true;
        } catch (Exception e) {
            canCloseJar = false;
        }
    }

    public MyClassLoader() {
        super(new URL[0], findParentClassLoader());
    }

    private static ClassLoader findParentClassLoader() {
        ClassLoader parent = Thread.currentThread().getContextClassLoader();
        System.out.println("context loader:" + parent);
        if (parent != null) {
            parent = parent.getParent(); // use appClassLoader rather than LaunchedClassLoader
        }
        if (parent == null) {
            parent = ClassLoader.getSystemClassLoader();
        }

        return parent;
    }

    @Override
    public void addURL(URL url) {
        if (!canCloseJar) {
            try {
                // 打开并缓存文件url连接
                URLConnection uc = url.openConnection();
                if (uc instanceof JarURLConnection) {
                    uc.setUseCaches(true);
                    ((JarURLConnection) uc).getManifest();
                    cachedJarFiles.add((JarURLConnection) uc);
                }
            } catch (Exception e) {
                // ignore
            }
        }
        super.addURL(url);
    }

    @Override
    public void close() throws IOException {
        if (canCloseJar) {
            try {
                super.close();
            } catch (IOException ioe) {
                // ignore
            }
        } else {
            for (JarURLConnection conn : cachedJarFiles) {
                conn.getJarFile().close();

            }
            cachedJarFiles.clear();
        }
    }
}
```

- web服务

```java
package com.stone.jdk.demohotswap;

import com.stone.jdk.demohotswap.classloader.MyClassLoader;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.lang.reflect.Method;
import java.net.JarURLConnection;
import java.net.URL;
import java.text.SimpleDateFormat;
import java.util.Date;

@SpringBootApplication
@RestController
public class DemoHotswapApplication extends SpringBootServletInitializer {
//public class DemoHotswapApplication {
    private static final String PROTOCOL_HANDLER = "java.protocol.handler.pkgs";

    public static void main(String[] args) {
        SpringApplication.run(DemoHotswapApplication.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(DemoHotswapApplication.class);
    }

    @RequestMapping("/")
    public String index() {
        return "Hello Mr Swap!";
    }

    @RequestMapping("/swap")
    public String swap() {
        MyClassLoader cl = new MyClassLoader();

        String jarFile = String.format("jar:file:%s/%s!/", "/tmp", "test.jar");
        try {
            // reproduce BUG un-comment below line
            URL jarUrl = new URL(jarFile);
            // FIX bug un-comment below line
            // URL jarUrl = new URL(null, jarFile, new sun.net.www.protocol.jar.Handler());
            cl.addURL(jarUrl);
        } catch (Exception e) {
            e.printStackTrace();
        }

        URL[] urls = cl.getURLs();
        if(urls != null && urls.length > 0) {
            try {
                // Create a jar URL connection object
                JarURLConnection jarURLConnection = (JarURLConnection) urls[0].openConnection();
                System.out.println("jarURLConnection:" + jarURLConnection);
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }

        String handlers = System.getProperty(PROTOCOL_HANDLER, "");
        System.out.println("handlers:" + handlers);

        String output = "NA";
        try {
            Class<?> swapClass = cl.loadClass("com.stone.jdk.demohotswap.swapClass.SwapMe");
            Object plugin = swapClass.newInstance();
            Method method = swapClass.getMethod("hello");
            output = (String)method.invoke(plugin);
            System.out.println("output:" + output);
        } catch (Exception ex) {
            ex.toString();
        } finally {
            try {
                cl.close();
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }

        return String.format("%s - %s", output,
                new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.sss").format(new Date()));
    }
}
```

- 热加载的类，注意这个类不要打包到测试的war包中。应该通过 `jar cvf test.jar com/stone/jdk/demohotswap/swapClass/SwapMe.class ` 单独打包

```java
package com.stone.jdk.demohotswap.swapClass;

/**
 * @Description:
 * @author: donghua
 * @date: 2019-09-22
 */
public class SwapMe {
    public String hello() {
        return "Swap Before!";
//        return "Swap After!";
    }
}
```

## 问题描述
上面代码build成spring boot war包部署到独立的jetty服务器没有问题，（包括jetty runner方式、start.jar等）。但是用`java -jar demo.jar"方式时出现java.lang.ClassFormatError。

```
org.springframework.web.util.NestedServletException: Handler dispatch failed; nested exception is java.lang.ClassFormatError: Truncated class file
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1054) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:942) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1005) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:897) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at javax.servlet.http.HttpServlet.service(HttpServlet.java:645) ~[javax.servlet-api-4.0.1.jar!/:4.0.1]
        at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:882) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at javax.servlet.http.HttpServlet.service(HttpServlet.java:750) ~[javax.servlet-api-4.0.1.jar!/:4.0.1]
        at org.eclipse.jetty.servlet.ServletHolder.handle(ServletHolder.java:876) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1623) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.websocket.server.WebSocketUpgradeFilter.doFilter(WebSocketUpgradeFilter.java:214) ~[websocket-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1610) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:99) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:118) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1610) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:92) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:118) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1610) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal(HiddenHttpMethodFilter.java:93) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:118) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1610) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:200) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:118) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.eclipse.jetty.servlet.ServletHandler$CachedChain.doFilter(ServletHandler.java:1610) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.servlet.ServletHandler.doHandle(ServletHandler.java:540) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ScopedHandler.handle(ScopedHandler.java:146) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.security.SecurityHandler.handle(SecurityHandler.java:548) ~[jetty-security-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.HandlerWrapper.handle(HandlerWrapper.java:132) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ScopedHandler.nextHandle(ScopedHandler.java:257) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
at org.eclipse.jetty.server.session.SessionHandler.doHandle(SessionHandler.java:1711) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ScopedHandler.nextHandle(ScopedHandler.java:255) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ContextHandler.doHandle(ContextHandler.java:1347) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ScopedHandler.nextScope(ScopedHandler.java:203) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.servlet.ServletHandler.doScope(ServletHandler.java:480) ~[jetty-servlet-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.session.SessionHandler.doScope(SessionHandler.java:1678) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ScopedHandler.nextScope(ScopedHandler.java:201) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ContextHandler.doScope(ContextHandler.java:1249) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.ScopedHandler.handle(ScopedHandler.java:144) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.handler.HandlerWrapper.handle(HandlerWrapper.java:132) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.Server.handle(Server.java:505) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.HttpChannel.handle(HttpChannel.java:370) ~[jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.server.HttpConnection.onFillable(HttpConnection.java:267) [jetty-server-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.io.AbstractConnection$ReadCallback.succeeded(AbstractConnection.java:305) [jetty-io-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.io.FillInterest.fillable(FillInterest.java:103) [jetty-io-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.io.ChannelEndPoint$2.run(ChannelEndPoint.java:117) [jetty-io-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.util.thread.QueuedThreadPool.runJob(QueuedThreadPool.java:781) [jetty-util-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at org.eclipse.jetty.util.thread.QueuedThreadPool$Runner.run(QueuedThreadPool.java:917) [jetty-util-9.4.19.v20190610.jar!/:9.4.19.v20190610]
        at java.lang.Thread.run(Thread.java:748) [na:1.8.0_171]
Caused by: java.lang.ClassFormatError: Truncated class file
        at java.lang.ClassLoader.defineClass1(Native Method) ~[na:1.8.0_171]
        at java.lang.ClassLoader.defineClass(ClassLoader.java:763) ~[na:1.8.0_171]
        at java.security.SecureClassLoader.defineClass(SecureClassLoader.java:142) ~[na:1.8.0_171]
 at java.net.URLClassLoader.defineClass(URLClassLoader.java:467) ~[na:1.8.0_171]
        at java.net.URLClassLoader.access$100(URLClassLoader.java:73) ~[na:1.8.0_171]
        at java.net.URLClassLoader$1.run(URLClassLoader.java:368) ~[na:1.8.0_171]
        at java.net.URLClassLoader$1.run(URLClassLoader.java:362) ~[na:1.8.0_171]
        at java.security.AccessController.doPrivileged(Native Method) ~[na:1.8.0_171]
        at java.net.URLClassLoader.findClass(URLClassLoader.java:361) ~[na:1.8.0_171]
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424) ~[na:1.8.0_171]
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357) ~[na:1.8.0_171]
        at com.stone.jdk.demohotswap.DemoHotswapApplication.swap(DemoHotswapApplication.java:64) ~[classes!/:0.0.1-SNAPSHOT]
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[na:1.8.0_171]
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[na:1.8.0_171]
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[na:1.8.0_171]
        at java.lang.reflect.Method.invoke(Method.java:498) ~[na:1.8.0_171]
        at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:190) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:138) ~[spring-web-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:104) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:892) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:797) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1039) ~[spring-webmvc-5.1.9.RELEASE.jar!/:5.1.9.RELEASE]
        ... 46 common frames omitted
```

重现步骤如下

1. git clone https://github.com/shidongwa/demo-hotswap.git
1. cd demo-hotswap 
1. mvn clean package
1. copy demo.war to you workspace
1. build and move test.jar to /tmp
  `jar cvf test.jar com/stone/jdk/demohotswap/swapClass/SwapMe.class`
  `cp test.jar /tmp/`
1. run demo.war in this way `java -jar demo.war`
1. access endpoint http://localhost:8080/swap
1. update SwapMe.java, package and replace test.jar
1. access endpoint http://localhost:8080/swap again
1. 上面的异常出现

*注意：* 如果以这种方式启动 `java -cp jetty-runner-9.4.14.v20181114.jar org.eclipse.jetty.runner.Runner demo.war` 不会有问题。

## 问题分析
比较下面两种jetty启动方式
- `java -jar demo.war` 是以Spring boot WarLauncher方式加载fat jar
- `java -cp jetty-runner-9.4.14.v20181114.jar org.eclipse.jetty.runner.Runner demo.war` 方式启动看当前线程ClassLoader是WebAppClassLoader而不是Spring boot LaunchedClassLoader
- 对应fat jar加载时打印jarFile url connection 是
`jarURLConnection:org.springframework.boot.loader.jar.JarURLConnection:jar:file:/tmp/test.jar!/`
- 对应jetty runner加载时打印jarFile url connetion 是`jarURLConnection:sun.net.www.protocol.jar.JarURLConnection:jar:file:/tmp/test.jar!/`
- 可以看出两种启动方式对应URLClassLoader加载jarFile的方式不同
- 从Spring boot社区看考虑性能问题，Spring boot loader不支持动态更新加载的jar文件。参考：https://github.com/spring-projects/spring-boot/issues/15964


## 问题解决
我们自定义URLClassLoader和Spring Boot fat jar是隔离和分开打包的。 通过在URLClassLoader加载jar包时指定sun.net.www.protocol.jar.Handler可以解决Spring
boot org.springframework.boot.loader.jar.JarURLConnection不能热更的问题。

## 参考
-   https://github.com/shidongwa/demo-hotswap