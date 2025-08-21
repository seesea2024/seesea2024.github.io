---
title: 不用max函数返回表中最大值或者最小值
categories: [iteye,sql]
layout: post
excerpt_separator: <!--more-->
---
不用max函数返回表中最大值或者最小值 <!--more-->

返回Test表中字段num的最大值或者最小值（不用Max函数）, 为了好玩。：)

---
+ 返回最大值 

```sql
select distinct num from Test where num not in ( select lesser.num from Test as greater,Test as lesser where lesser.num<greater.num)  
```

+ 返回最小值 

```sql
select distinct num from Test where num not in ( select greater.num from Test as greater,Test as lesser where lesser.num<greater.num)     
```

原因是以下SQL返回的结果集中，lesser.num中包括所有num除了最大值，greater.num中包括所有num除了最小值： 

```sql
select lesser.num,greater.num from Test as lesser, Test as greater where lesser.num < greater.num;  
```
