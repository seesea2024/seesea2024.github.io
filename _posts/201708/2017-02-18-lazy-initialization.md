---
title: "lazy loading实现"
categories: [iteye, lazy initialization]
layout: post
excerpt_separator: <!--more-->
---
在进行O-R mapping时经常会碰到加载a graph of objects. 这是lazy loading应用的场景。如果不想把所有数据库中关联的表对象都一次性load到内存，可以参考下面的解决方法。<!--more-->

### lazy initialization
简单来说就是使用前判断目标对象是否为null，是则真正从数据库加载。缺点是非null时并不总是代表对象加载了。还有一个缺点是domain类中加入了调用数据库的逻辑。  

### virtual proxy     
主要考虑one-many关系，对应有一个List或者Set需要做lazy loading. 一般可以继承List/Set, 并在继承类中引用实际的List/Set 对象source(通过getSource() lazy loading)。继承的任何List/Set接口比如size(),iterator(),都通过getSource()检查是否已经初始化了，没有的话再从数据库中加载。  

### value holder     
通过getCollectionA()接口隐藏实现。getCollectionA实际上是从value holder中获取目标Collection. value holder中封装了是否初始化了目标，数据库操作等。相对于第一种方法一个好处是数据库操作封装在value holder中，domain 类中不用关心了。  
