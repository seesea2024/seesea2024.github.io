---
title: redis版本特性
date: 2017-01-24 23:19:04 Z
categories:
- blog
layout: post
---
Reids各版本特性

### Redis 4 feature 

* module
* psync2
* LFU eviction policy
* mixed rdb-aof format
* del/flushall/flushdb async
* MEMORY command
* CLUSTER DOCKER/NAT support

### redis 3
* Redis Cluster

### redis 2.8
* psync
* Slaves explicitly ping masters now, a master is able to detect a timed out
        slave independently.
* Masters can stop accepting writes if not enough slaves with a given
        maximum latency are connected
* Keyspace changes notifications via Pub/Sub
* CONFIG SET maxclients is now available
* Ability to bind multiple IP addresses
* Set process names so that you can recognize, in the "ps" command output,
        the listening port of an instance, or if it is a saving child
* Automatic memory check on crash
* CONFIG REWRITE is able to materialize the changes in the configuration
        operated using CONFIG SET into the redis.conf file
* PUBSUB command for Pub/Sub introspection capabilities
* EVALSHA can now be replicated as such, without requiring to be expanded
        to a full EVAL for the replication link
* Better Lua scripts error reporting
* SDIFF performance improved


### redis 2.6
1. 支持lua脚本。
2. VM（虚拟内存）去掉了。
3. 对于client的limit限制变成无限制。
4. aof性能提升了不少。
5. key的过滤时间可以支持毫秒级别了，原来是秒。
6. list与hash 的属性filed或value包含小整数，内存优化列好（使用了jemalloc，以前是malloc）。
7. 提供了BITCOUNT与BITOP，前者支持位值count，后者支持了位操作。（以前只支持key-value 的置位操作）
8. 支持新命令dump以及restore ，即序列化与反序列化操作。
9. 大数据存储性能优化
