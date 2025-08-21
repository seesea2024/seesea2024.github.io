---
title: redis分布式锁 - redlock
date: 2017-03-27 21:32:31 Z
categories:
- midware
layout: post
---

摘自：<http://mushroom.cnblogs.com/>

## 单机Redis分布式锁

```
# 加锁
SET resource_name my_random_value NX PX 30000
```

```lua
# 释放锁
if redis.call("get",KEYS[1]) == ARGV[1] then
        return redis.call("del",KEYS[1])
    else
        return 0
    end
```

argv[1]需要是random value，避免误删别的客户端锁。问题场景如下：

1. A客户端拿到对象锁，但在因为一些原因被阻塞导致无法及时释放锁。
2. 因为过期时间已到，Redis中的锁对象被删除。
3. B客户端请求获取锁成功。
4. A客户端此时阻塞操作完成，删除key释放锁（B的锁）
5. C客户端请求获取锁成功。
6. 这时B、C都拿到了锁，因此分布式锁失效。

单机分布式锁是单点，redis主从复制是异步的有延迟。问题场景如下：

1. 客户端A在master节点拿到了锁。
2. master节点在把A创建的key写入slave之前宕机了。
3. slave变成了master节点 4.B也得到了和A还持有的相同的锁（因为原来的slave里还没有A持有锁的信息）

## Redlock
在多台master情况下实现这个算法，并保证锁的安全性。 步骤如下：

1. 客户端以毫秒为单位获取当前时间。
2. 使用同样key和值，循环在多个实例中获得锁。 为了获得锁，客户端应该设置个偏移时间，它小于锁自动释放时间(即key的过期时间)。 举个例子来说，如果一个锁自动释放时间是10秒，那偏移时间应该设置在5~50毫秒的范围。 防止因为某个实例崩溃掉或其他原因，导致client在获取锁时耗时过长。
3. 计算获取所有锁的耗时，即当前时间减去开始时间，得到a值。 用锁自动释放时间减去a值，在减去偏移时间，得到c值，如果获取锁成功的实例数量大于实际的数量一半，并且c大于0，那么锁就被获取成功。
锁获取成功，锁对象的有效时间是上面的c值。
4. 若是客户端因为一些原因获取失败，原因可能是上面的c值为负数或者锁成功的数量小于实例数，以用N/2+1当标准(N为实例数)。 那么会释放所有实例上的锁。

```
//锁自动释放时间
TimeSpan ttl=new TimeSpan(0,0,0,30000)
//获取锁成功的数量
 int n = 0; 
//记录开始时间
 var startTime = DateTime.Now;

//在每个实例上获取锁
for_each_redis(
    redis =>
    {
        if (LockInstance(redis, resource, val, ttl)) n += 1;
    }
);

//偏移时间是锁自动释放时间的1%，根据上面10s是5-50毫秒推出。
 var drift = Convert.ToInt32(ttl.TotalMilliseconds * 0.01); 

//锁对象的有效时间=锁自动释放时间-(当前时间-开始时间)-偏移时间
 var validity_time = ttl - (DateTime.Now - startTime) - new TimeSpan(0, 0, 0, 0, drift);

//判断成功的数量和有效时间c值是否大于0 if (n >= (N/2+1) && validity_time.TotalMilliseconds > 0) { }
```