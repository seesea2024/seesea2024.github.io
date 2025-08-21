---
title: 返回奇数行或者偶数行数据库表记录
categories: [iteye, sql]
layout: post
excerpt_separator: <!--more-->
---
Oracle返回奇数行或者偶数行数据库表记录<!--more-->

### 方法1用Decode函数 
------
for even number of records 

```sql
select * from emp where rowid in (select decode(mod(rownum,2),0,rowid) from emp);
```

for odd number of records 

```sql
select*from emp where rowid in (select decode(mod(rownum,2),1,rowid) from emp); 
```

或者

FOR ODD NUMBER OF ROWS 

```sql    
    SELECT * FROM emp WHERE rowid IN (SELECT DECODE(MOD(rownum,2),1,rowid,NULL) FROM emp); 
```

FOR EVEN NUMBER OF ROWS 

```sql
SELECT * FROM emp WHERE rowid IN (SELECT DECODE(MOD(rownum,2),0,rowid,NULL) FROM emp); 
```

### 方法2用in子查询 

------

Odd number of records: 

```sql
select * from emp where (rowid,1) in (select rowid, mod(rownum,2) from emp); 
```

Output:- 1 3 5 

Even number of records: 

```sql
select * from emp where (rowid,0) in (select rowid, mod(rownum,2) from emp) 
```

Output:- 2 4 6  
