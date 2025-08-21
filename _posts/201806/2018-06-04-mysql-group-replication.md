---
layout: post
title: mysql主从复制
categories: [mysql]
---
## mysql 主从复制
mysql有异步复制，半同步复制，组复制等。

### 异步复制
主mysql提交，再异步复制到从mysql

![异步复制](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/async_repl.png?raw=true)

### 半同步复制
主mysql同步从mysql，至少一个从mysql relay log写成功时，主mysql提交。

![半同步复制](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/semi_repl.png?raw=true)

### 组复制
单主和多主mysql模式，基于paxos协议，只要大多数主机可用，则服务可用。
单主模式下，会自动选主，所有更新操作都在主上进行；多主模式下，所有 server 都可以同时处理更新操作。

![组复制](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/group_repl.png?raw=true)

![单主](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/group_repl_single_master.png?raw=true)

![多主](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/group_repl_multi_master.png?raw=true)

### 参考
http://blog.51cto.com/ylw6006/1971139

斩月是个神人，最早接触TFS（Taobao File System）就是看他的博客开始的。