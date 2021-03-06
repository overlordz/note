> 什么是分布式锁？

分布式系统中控制多个进程对共有资源的访问

> 分布式锁具有的性质

- 互斥性：同一时刻，只有一个客户端可以获取锁
- 安全性：锁的获取和释放是同一个客户端
- 可用性：高可用的分布式锁系统及避免产生死锁

> Redis实现分布式锁

一般处理：

**setnx + expire 加锁**

setnx 如果key 不存在，set成功返回1，失败返回0，保证互斥

expire 设置过期时间，防止锁的长期持有或死锁

缺点是：当 setnx 成功之后发送异常，导致expire失败，产生死锁



最优处理：

**set key value NX EX expire-time**

Redis 2.6.12 以后，直接使用set方法进行加锁，避免死锁。

NX和EX为固定值，前者表示 SET IF NOT Exist ，后者表示添加过期时间，具体时间取最后一个参数



> 比较删除解锁

DEL key 前判断是否为加锁的客户端，可以通过添加客户端的唯一id（具体实现自己决定）到 value 中，每次删除前比较，一致再进行删除解锁，保证安全性。





#### 缓存穿透

用户访问一个本身就不存在的缓存，直接访问到数据库

处理方式：

1. 使用布隆过滤器判断缓存中是否存在
2. 为请求设置一个值为null的缓存，设置较短的过期时间



#### 缓存击穿

用户访问一个已过期的缓存，数据库进行缓存写入

处理方式：

1. 热点数据的缓存永不过期
2. 采用分布式锁，缓存失效后只有一个线程更新并写入

#### 缓存雪崩

大面积缓存击穿或服务不可用

处理方式：

1. 使用Redis哨兵或集群等框架提升可用性
2. 采用和缓存击穿一样的方式
3. 错开缓存数据的过期时间点，防止缓存大面积失效







#### Redis 使用



分布式数据库分库分表 ，自增 id 没有办法靠MySQL，只能通过Redis中的incr 的id值

上面这样操作，会造成Redis得使用，优化思路：

incrby orderId 100，一次性生成100个，然后放在缓存里面，自增获取自增的id值