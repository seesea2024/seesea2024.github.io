---
layout: post
title: spring bean初始化顺序
categories: [spring]
---
spring bean初始化顺序（不包括BeanFactory的创建/属性注入准备）

Constructor（构造函数） > @PostConstruct > InitializingBean > init-method

代码验证如下。主要注意的是@PostConstruct执行的InitDestroyAnnotationBeanPostProcessor的postProcessBeforeInitialization
```java
/**
 * @Author: shidonghua
 * @Description:
 * @Date: 10/16/20 11:38
 * @Version: 1.0
 * https://programmer.help/blogs/after-properties-set-and-init-method-postconstruct-of-spring-initializing-bean.html
 * postConstruct 对应的是BeanPostProcessor的postProcessBeforeInitialization
 * 执行顺序是：Constructor > @PostConstruct > InitializingBean > init-method -> preDestroy -> destroy -> destroy-method
 */
public class InitSequenceBean implements InitializingBean, DisposableBean {

    public InitSequenceBean() {
        System.out.println("InitSequenceBean: constructor");
    }

    @PostConstruct
    public void postConstruct() {
        System.out.println("InitSequenceBean: postConstruct");
    }

    @PreDestroy
    public void preDestory() {
        System.out.println("InitSequenceBean: preDestroy");
    }

    public void initMethod() {
        System.out.println("InitSequenceBean: init-method");
    }

    public void destroyMethod() {
        System.out.println("InitSequenceBean: destroy-method");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("InitSequenceBean: afterPropertiesSet");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("InitSequenceBean: destroy");
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <bean class="com.stone.studio.springdemo.lifecycle.InitSequenceBean" init-method="initMethod"
          destroy-method="destroyMethod"/>

</beans>
```


## 参考
- [github](https://github.com/shidongwa/spring-demo.git)
- [参考](https://programmer.help/blogs/after-properties-set-and-init-method-postconstruct-of-spring-initializing-bean.html)