---
title: start with....connect by...
categories: [iteye，sql]
layout: post
excerpt_separator: <!--more-->
---
Oracle start with <!--more-->

```sql
    select * from tablename start with cond1 connect by cond2 where cond3;
```

简单说来是将一个树状结构存储在一张表里，比如一个表中存在两个字段: id,parentid。那么通过表示每一条记录的parent是谁，就可以形成一个树状结构。 用上述语法的查询可以取得这棵树的所有记录。 其中COND1是根结点的限定语句，当然可以放宽限定条件，以取得多个根结点，实际就是多棵树。 COND2是连接条件，其中用PRIOR表示上一条记录，比如 CONNECT BY PRIOR ID=PRAENTID就是说上一条记录的ID是本条记录的PRAENTID，即本记录的父亲是上一条记录。 COND3是过滤条件，用于对返回的所有记录进行过滤.

比如我们需要在Oracle中存储一个树形的menu结构，可以添加两个字段MENU_ID与MENU_PID 如果这个菜单项是一级菜单，那么我们把它的MENU_PID设为0 如果这个菜单是另一个菜单的子菜单，那么我们就把它的MENU_PID设为它的父菜单的MENU_ID。 有了这样的结构，我们一个递归就能把这颗“树”显示出来。 

```sql
    SELECT * FROM T_SYS_MENU STARTWITH MENU_PID=0 CONNECT BY PRIOR MENU_ID=MENU_PID order by MENU_ID
```