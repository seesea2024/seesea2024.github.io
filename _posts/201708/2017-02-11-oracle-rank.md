---
title: rank() analytic function
categories: [iteye,sql]
layout: post
excerpt_separator: <!--more-->
---
Oracle rank<!--more-->

As an analytic function, RANK computes the rank of each row returned from a query with respect to the other rows returned by the query, based on the values of the value_exprs in the order_by_clause.

返回工资第二高的员工

```sql
SELECT * FROM (   
    SELECT employee_id, last_name, salary,   
        RANK() OVER (ORDER BY salary DESC) EMPRANK   
    FROM employees)
WHERE emprank = 2; 
```

verify result 

```sql
SELECT employee_id, last_name, salary, RANK() OVER (ORDER BY salary DESC) EMPRANK FROM employees;   
```

另外一个例子 

```sql
SELECT department_id,last_name,salary,commission_pct, 
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC, commission_pct RANK 
FROM employees;
```
