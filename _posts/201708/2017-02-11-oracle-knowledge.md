---
title: Oracle 笔记
categories: [iteye,oracle]
layout: post
excerpt_separator: <!--more-->
---
Oracle 笔记 <!--more-->

### rownum and rowid

rownum是在得到结果集的时候产生的，用于标记结果集中结果顺序的一个字段，这个字段被称为“伪数列”，也就是事实上不存在的一个数列。它的特点是按顺序标记，而且是逐次递加的，换句话说就是只有有rownum=1的记录，才可能有rownum=2的记录。   

和rownum相似，oracle还提供了另外一个伪数列：rowid。不过rowid和rownum不同，一般说来每一行数据对应的rowid是固定而且唯一的，在这一行数据存入数据库的时候就确定了。可以利用rowid来查询记录，而且通过rowid查询记录是查询速度最快的查询方法。(这个我没有试过，另外要记住一个长度在18位，而且没有太明显规律的字符串是一个很困难的事情，所以我个人认为利用rowid查询记录的实用性不是很大)rowid只有在表发生移动(比如表空间变化，数据导入/导出以后)，才会发生变化。顺便提一下，Oracle中的伪数列有：**rownum,rowid,nextval,currval**  

### left join / right join 

```sql
tableA left join tableB on
```
相当与 

```sql
from tableA,tableB where tableA.id=tableB.id(+) 
```

最终记录数与tableA记录数相同   

```sql
tableA right join tableB on 
```

最终记录数与tableB记录数相同   

### truncate 与 delete 区别 

truncate：自动commit, 不能rollback，速度快，是DDL 
delete：程序员控制commit，commit前可以rollback，速度慢，是DML，   

### PL/SQL function与Procedure区别   

Function and Procedure both are PL/SQL blocks, main difference between function and procedure is - Function has to return some value using return clause whereas procedure may or may not return any value( no out parameter). We can use functions in SQL query but can't use procedure.  
