> 原文地址 [learnku.com](https://learnku.com/articles/39265)

### 背景

最近公司出了一起故障，问题代码如下：

```php
    /**
     * TRUE: 触发限流，FALSE：未触发限流
     */
    public function acquire() {
        try {
            $redisHandler = $this->redisInstance->getHandler();
            $redisHandler->set($this->rateLimitKey, $this->tokenNum, ['nx', 'ex' => $this->expireTime]);
            $leftTokenNum = $redisHandler->decr($this->rateLimitKey);
            if ($leftTokenNum < 0) {
                return TRUE;
            }
            return FALSE;
        } catch (\Exception $e) {
            return FALSE;
        }
    }

```

作者的目的是针对爆款商品的购买，使用 redis 来起到一个限流的作用，1 秒钟只允许 1 人购买。

结果上线过后不久，运营就反馈线上出故障了，该爆款商品所有人都不能购买了。

### 分析

上面代码的思路很简单：通过 `$redis->set('key', '1', ['nx', 'ex'=>1]);` 命令，设置值为 1 过期时间为 1 秒的计数器，基于该计数器的扣减来达到 1 秒钟放行 1 个请求的目的。

### 测试

我们简化一下上面的代码，

```php
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);

$key = 'test_redis_key';
$redis->set($key, '1', ['nx', 'ex' => 1]);
$left = $redis->decr($key);

if ($left < 0) {
  // 这里通过状态码来更方便的观察
  header('Is-Limited:1', true, 500);
} else {
  header('Is-Limited:0', true, 200);
}

```

简化后使用 siege 模拟 100 个用户并发压测一下。  
[](https://cdn.learnku.com/uploads/images/202001/05/21857/RWB6IZv9Ka.gif!large)

[![](https://cdn.learnku.com/uploads/images/202001/05/21857/RWB6IZv9Ka.gif!large)](https://cdn.learnku.com/uploads/images/202001/05/21857/RWB6IZv9Ka.gif!large)

非常稳啊，1 秒钟通过 1 个请求。  
我们的开发同学也就是经过了上述测试才放心把代码发上线的，咋一上线就炸了呢？

### 原因

我们来看下面一段操作，

```
[root@e98dffb83384 src]# ./redis-cli
127.0.0.1:6379> SETNX k 1
(integer) 1
127.0.0.1:6379> EXPIRE k 10 # 为了方便演示，这里设置 10 秒过期时间
(integer) 1
127.0.0.1:6379> DECR k # 在过期时间内，第一次扣减成 0
(integer) 0
127.0.0.1:6379> DECR k # 继续扣减成 -1
(integer) -1
127.0.0.1:6379> DECR k # 继续扣减成 -2
(integer) -2
127.0.0.1:6379> TTL k # k 还有 2 秒过期
(integer) 2
127.0.0.1:6379> DECR k # 继续扣减成 -3
(integer) -3
127.0.0.1:6379> TTL k # 距离设置过期时间 10 秒之后，k 已经过期
(integer) -2
127.0.0.1:6379> DECR k # 这时候再扣减发现 k 的值被扣减成 -1 
(integer) -1
127.0.0.1:6379> DECR k # 继续扣减成 -2
(integer) -2
127.0.0.1:6379> TTL k # 查看 k 过期时间是永不过期
(integer) -1
127.0.0.1:6379> SETNX k 3 # 再设置是不成功的
(integer) 0
127.0.0.1:6379> DECR k # 继续扣减成 -3
(integer) -3

```

在 Redis key 未过期之前，`DECR` 命令都是正常扣减的。一旦 key 过期了，再执行 `DECR` 命令，会发现 key 的值和过期时间都变为 -1 了。

Redis 官网对 `DECR` 命令介绍里有这么一段：

> Decrements the number stored at key by one. If the key does not exist, it is set to 0 before performing the operation.

对于出问题的代码，

```
$redisHandler->set($this->rateLimitKey, $this->tokenNum, ['nx', 'ex' => $this->expireTime]);
$leftTokenNum = $redisHandler->decr($this->rateLimitKey);

```

假设在第一句 `SETNX` 之后第二句 `DECR` 之前，key 过期了，再执行 `DECR` 就会先生成一个永不过期值为 0 的 key。

之后所有请求的 `SETNX` 都是 fasle，一直会基于这个永不过期的 key 进行递减，所有的 `$leftTokenNum` 都小于 0，因此导致所有请求被限流。

### 问题复现

自测时为啥发现不了问题？因为自测时设置的过期时间是 1 秒，导致 key 在两步之间过期出现的概率很小。我们只要将过期时间调的足够小，很容易复现问题。

把过期时间改为 5 毫秒，

```
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);

$key = 'test_redis_key';
$redis->set($key, '3', ['nx', 'px' => 5]); // key 设置成 5 毫秒过期
$left = $redis->decr($key);

if ($left < 0) {
  // 这里通过状态码来更方便的观察
  header('Is-Limited:1', true, 500);
} else {
  header('Is-Limited:0', true, 200);
}

```

依然使用 siege 压测：  
[](https://cdn.learnku.com/uploads/images/202001/05/21857/bRyFKd010f.gif!large)

[![](https://cdn.learnku.com/uploads/images/202001/05/21857/bRyFKd010f.gif!large)](https://cdn.learnku.com/uploads/images/202001/05/21857/bRyFKd010f.gif!large)

由于设置的 5 毫秒放行一个请求，因此前半部分基本上都是通过的请求，偶尔有几个限流的，这是正常的。  
但是没过多久，所有请求都被限流了，也就复现了线上的故障。

### 解决方案

如何改进代码来正确的实现限流呢？

Redis 的 [`EVAL` 命令](https://redis.io/commands/eval) 执行 Lua 脚本时可以保证原子性。

> Atomicity of scripts  
> Redis uses the same Lua interpreter to run all the commands. Also Redis guarantees that a script is executed in an atomic way: no other script or Redis command will be executed while a script is being executed.

`EVAL` 命令的格式为：

> EVAL script numkeys key [key ...] arg [arg ...]

例子：

```
> eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
1) "key1"
2) "key2"
3) "first"
4) "second"

```

我们可以借助 Lua 脚本来避免 `SETNX` 和 `DECR` 之间会出现过期的尴尬情况。

```php
        $redis = new Redis();
        $redis->connect('127.0.0.1', 6379);

        $key = 'test_redis_key1';

        $script = <<<LUA
local max = tonumber(ARGV[1])
local interval_milliseconds = tonumber(ARGV[2])
local current = tonumber(redis.call('get', KEYS[1]) or 0)

if (current + 1 > max) then
    return true
else
    redis.call('incrby', KEYS[1], 1)
    if (current == 0) then
        redis.call('pexpire', KEYS[1], interval_milliseconds)
    end
    return false
end
LUA;

        $redis->script('load', $script);
        $isLimited = $redis->eval($script, [$key, 1, 5], 1); // key 5 毫秒过期

        if ($isLimited) {
            header('Is-Limited:1', true, 500);
        } else {
            header('Is-Limited:0', true, 200);
        }

```

依然使用 siege 压测，  
[](https://cdn.learnku.com/uploads/images/202001/05/21857/t1DULIxU6G.gif!large)

[![](https://cdn.learnku.com/uploads/images/202001/05/21857/t1DULIxU6G.gif!large)](https://cdn.learnku.com/uploads/images/202001/05/21857/t1DULIxU6G.gif!large)

持续压了 10 多分钟也没出现之前问题，问题得以解决。

### 总结

*   Redis 中 `DECR` 一个不存在的 key 会先把 key 值设置为 0 , TTL 设置为 -1 (永不过期)，再进行减 1 操作。
*   使用 `SETNX` 配合 `DECR` 实现限流，会出现 key 永不过期情况。过期时间比较小或者高并发情况下，发生概率更高。
*   在 Redis 中执行 Lua 脚本是原子操作。
*   可以通过 Redis + Lua 实现高并发下的限流。

