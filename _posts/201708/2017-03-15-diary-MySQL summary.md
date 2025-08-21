---
title: MySQL summary
date: 2017-03-15 18:32:31 Z
categories:
- diary
layout: post
---

# MySQL summary

## 索引
从两个维度来看：

* 普通索引
* 唯一索引
* 主键索引
* 组合索引
  1. 索引遵循最左匹配规则
* 函数索引
  1. mysql不支持

---
* B+树索引
* hash索引
  1. 只能是等值查询
  2. 不支持范围查询
  2. 内存DB中用的多一些？
* Covering Index(覆盖索引）
  1. 在Explain的时候，输出的Extra信息中如果有“Using Index”，就表示这条查询使用了覆盖索引
  2. 覆盖索引(covering index)，MySQL只需要通过索引就可以返回查询所需要的数据，而不必在查到索引之后再去查询数据
  3. 不要select *, 应该select 覆盖索引中指定的字段
* FULLTEXT全文索引

## CPU负载高问题分析
### slow SQL
```
mysql> show variables like '%slow%'; 
| Variable_name    | Value  |
| ------------     | ------ |
　log_slow_queries | ON 
　slow_launch_time | 2 　
```

### 最大连接数
```
mysql> show variables like 'max_connections'; 
　　+-----------------+-------+ 
　　| Variable_name | Value | 
　　+-----------------+-------+ 
　　| max_connections | 256 | 
　　+-----------------+-------+　
```

### 显示连接
```
mysql> show full processlist;  
+---------+-------------+--------------------+----------------+-------------+-------+-----------------------------------------------------------------------+-----------------------+  
| Id      | User        | Host               | db             | Command     | Time  | State                                                                 | Info                  |  
+---------+-------------+--------------------+----------------+-------------+-------+-----------------------------------------------------------------------+-----------------------+  
| 1056536 | replication | 192.168.6.91:38417 | NULL           | Binlog Dump | 33759 | Master has sent all binlog to slave; waiting for binlog to be updated | NULL                  |  
| 1107067 | miaohr      | 192.168.6.81:32024 | NULL           | Query       |     0 | NULL                                                                  | show full processlist |  
| 1107182 | miaohr      | 192.168.6.91:44217 | hr_db_business | Sleep       |     1 |                                                                       | NULL                  |  
```

### 显示状态
```
mysql> show status;  
+------------------------------------------+----------------------+  
| Variable_name                            | Value                |  
+------------------------------------------+----------------------+  
| Aborted_clients                          | 777                  |  
| Aborted_connects                         | 16                   |  
| Binlog_cache_disk_use                    | 532                  |  
```