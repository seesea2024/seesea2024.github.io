
# java锁机制
-----------
## 乐观锁/悲观锁

* java中的乐观锁主要有自旋锁、轻量级锁与偏向锁，基本都是通过CAS操作实现的

* java中的悲观锁就是Synchronized,AQS框架下的锁则是先尝试cas乐观锁去获取锁，获取不到，才会转换为悲观锁，如RetreenLock

## markword对象头

状态 | 标志位 | 存储内容
---|--- |---
未锁定 | 01 | 对象哈希码、对象分代年龄
轻量级锁定 | 00 | 指向锁记录的指针
膨胀(重量级锁定) | 10 | 执行重量级锁定的指针
GC标记 | 11	| 空(不需要记录信息)
可偏向 | 01	| 偏向线程ID、偏向时间戳、对象分代年龄

![markword lock](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/java_markword_lock2.png?raw=true)


### 偏向锁
-XX:+UseBiasedLocking -XX:BiasedLockingStartupDelay=0

### 自旋锁

### 轻量锁（先自旋）
![markword lock](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/java_light_lock.jpeg?raw=true)

### 重量锁（synchronized）
![synchronized实现](https://github.com/shidongwa/seesea2024.github.io/blob/master/images/java_synchronized.png?raw=true)

- Contention List：竞争队列，所有请求锁的线程首先被放在这个竞争队列中；
- Entry List：Contention List中那些有资格成为候选资源的线程被移动到Entry List中；
- Wait Set：哪些调用wait方法被阻塞的线程被放置在这里；
- OnDeck：任意时刻，最多只有一个线程正在竞争锁资源，该线程被成为OnDeck；
- Owner：当前已经获取到锁资源的线程被称为Owner；

#### synchronized的执行过程： 
1. 检测Mark Word里面是不是当前线程的ID，如果是，表示当前线程处于偏向锁 
2. 如果不是，则使用CAS将当前线程的ID替换Mard Word，如果成功则表示当前线程获得偏向锁，置偏向标志位1 
3. 如果失败，则说明发生竞争，撤销偏向锁，进而升级为轻量级锁。 
4. 当前线程使用CAS将对象头的Mark Word替换为锁记录指针，如果成功，当前线程获得锁 
5. 如果失败，表示其他线程竞争锁，当前线程便尝试使用自旋来获取锁。 
6. 如果自旋成功则依然处于轻量级状态。 
7. 如果自旋失败，则升级为重量级锁。


偏向锁是在无锁争用的情况下使用的，也就是同步代码执行时没有其它线程会执行该同步块。一旦有了第二个线程的争用，偏向锁就会升级为轻量级锁。如果轻量级锁自旋到达阈值后，没有获取到锁，就会升级为重量级锁；