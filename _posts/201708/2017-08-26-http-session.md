---
title: httpsession
categories: frontend
layout: post
published: true
---
### 一般用来实现Session的方法有两种

1. URL重写
Web Server在返回Response的时候，检查页面中所有的URL，包括所有的连接，和HTML Form的Action属性，在这些URL后面加上“;jsessionid=XXX”。 
下一次，用户访问这个页面中的URL。jsessionid就会传回到Web Server。

1. Cookie
如果客户端支持Cookie，Web Server在返回Response的时候，在Response的Header部分，加入一个“set-cookie: jsessionid=XXXX”header属性，把jsessionid放在Cookie里传到客户端。 

客户端会把Cookie存放在本地文件里，下一次访问Web Server的时候，再把Cookie的信息放到HTTP Request的“Cookie”header属性里面，这样jsessionid就随着HTTP Request返回给Web Server。

### 服务端／客户端session存储

使用服务端还是客户端session存储要看应用的实际情况的。一般来说不要求用户注册登录的公共服务系统（如google）采用cookie做客户端session存储（如google的用户偏好设置），而有用户管理的系统则使用服务端存储。原因很显然：无需用户登录的系统唯一能够标识用户的就是用户的电脑，换一台机器就不知道谁是谁了，服务端session存储根本不管用；而有用户管理的系统则可以通过用户id来管理用户个人数据，从而提供任意复 杂的个性化服务；

客户端和服务端的session存储在性能、安全性、跨站能力、编程方便性等方面都有一定的区别，而且优劣并非绝对（譬如 TheServerSide 号称不使用HttpSession，所以性能好，这很显然：一个具有上亿的访问用户的系统，要在服务端数据库中检索出用户的偏好信息显然是低效的，Session管理器不管用什么数据结构和算法都要耗费大量内存和CPU时间；而用cookie，则根本不用检索和维护session数据，服务器可 以做成无状态的，当然高效）；**TSS是用客户端 cookie存储session？？**
