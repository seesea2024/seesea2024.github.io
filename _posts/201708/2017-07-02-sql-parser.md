---
title: SQL Parser
date: 2017-07-02 21:32:31 Z
categories: middleware
layout: post
---

分库分表框架设计过程中，不可避免要考虑到SQL Parser功能的实现。原因有：
1. 需要取出sharding key来路由到正确的数据库节点（数据源）
2. 分库分表后SQL需要进行改写，比如逻辑表到物理表
3. 原SQL中语句功能在分库分表环境下需要重新实现，比如排序，分页，取平均值，distinct，join等

### 现有的SQL Parser开源方案有

1. JSQLParser
jsqlparser 用的是 JavaCC 的方式解析 SQL，相对于 Druid 来说，性能比较低。

2. Druid
Druid 采用的词法和语法分析的方式解析 SQL(Lexer -> Parser -> AST -> Vistor)，编码工作量大，但性能会提升很多。Druid 的 SQL 解析器对于开发者而言稍微有些不易上手。当当sharding-jdbc，tddl部分版本采用Druid作为SQL Parser


3. ANTLR
TDDL部分版本采用了ANTLR作为SQL Parser

忘了正则表达式吧，性能是个问题

