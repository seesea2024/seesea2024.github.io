---
title: Oracle Decode用法
categories: [iteye,sql]
layout: post
excerpt_separator: <!--more-->
---
oracle decode <!--more-->

from: http://www.adp-gmbh.ch/ora/sql/decode.html 

```sql
decode (expression, search_1, result_1)
decode (expression, search_1, result_1, search_2, result_2)
decode (expression, search_1, result_1, search_2, result_2, ...., search_n, result_n)
decode (expression, search_1, result_1, default)
decode (expression, search_1, result_1, search_2, result_2, default)
decode (expression, search_1, result_1, search_2, result_2, ...., search_n, result_n, default)

```
decode compares expression to the search_x expressions and, if matches, returns result_x. If not, returns default, or, if default is left out, null. 

a sample: 

```sql
create table a (x int, b int);

insert into a values (1,2);
insert into a values (1,3);
insert into a values (2,4);
insert into a values (2,5);
insert into a values (3,6);

select * from a where b = decode (x,1,2,2,4);
          X          B
---------- ----------
         1          2
         2          4
```
