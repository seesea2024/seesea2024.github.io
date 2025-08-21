---
title: 如何删除数据库表中相同记录
categories: [iteye,sql]
layout: post
excerpt_separator: <!--more-->
---
如何删除表中相同字段值的记录（比如相同的EMPNO，假设没有主键）<!--more-->

#### 方法1 
  
```sql  
DELETE FROM EMP WHERE ROWID NOT IN(SELECT MAX(ROWID) FROM EMP GROUP BY EMPNO);
```

#### 方法2  

```sql
DELETE FROM emp e WHERE ROWID NOT IN ( SELECT MIN(ROWID) FROM emp a WHERE e.empno=a.empno);
```

#### 方法3

```sql
DELETE FROM table_name A WHERE ROWID > ( SELECT min(ROWID) FROM table_name B WHERE A.col = B.col);
```
