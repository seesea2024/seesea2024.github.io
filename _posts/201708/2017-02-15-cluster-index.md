---
title: cluster nad non-cluster index
categories: [index, database]
layout: post
excerpt_separator: <!--more-->
---
clustered and nonclustered indexes <!--more-->

A clustered index is a special type of index that reorders the way records in the table are physically stored. Therefore table can have only one clustered index. The leaf nodes of a clustered index contain the data pages. 

A nonclustered index is a special type of index in which the logical order of the index does not match the physical stored order of the rows on disk. The leaf node of a nonclustered index does not consist of the data pages. Instead, the leaf nodes contain index rows.   

CLUSTER是段的一种类型。一个聚簇就是一个段，聚簇中的所有表根据聚簇列存放在同一个段中。这些表具有相同的物理存储结构。CLUSTER的主要优点包括： 
+ 由于不同表的数据根据CLUSTER键的顺序存放在一起，因此当对CLUSTER表之间连接访问时，会减少IO和存储访问时间；   
+ 虽然每个表都包含CLUSTER键，但这一列在CLUSTER中保存一份。因此可以减少存储的空间。   

适合CLUSTER的表的特点：   
+ 表以查询为主，不适合以插入或修改为主的表；  
+ 经常通过连接一起查询的表。   

对于索引 CLUSTER，注意首先创建CLUSTER，然后创建相应的CLUSTER表，最后一定要建立CLUSTER索引，如果不建立索引的话，没有任何记录可以插入到CLUSTER表中。
