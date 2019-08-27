---
title: PHP 操作 Redis
tags:
 - Redis
 - PHP


categories: Redis
abbrlink: 29270
date: 2017-04-30 10:04:02
---

### PHP 操作 Redis

> - Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。
> - Redis 将数据库完全保存在内存中，仅使用磁盘进行持久化。所以可以实现非常高的读写操作；
> - Redis 跟其他键值存储的数据相比，具有丰富的数据类型。
> - Redis 可以将数据复制到任意的从机中。
> - 在一些需要大容量数据集的应用，Redis也并不适合，因为它的数据集不会超过系统可用的内存。所以如果你有大数据应用，而且主要是读取访问模式，那么Redis并不是正确的选择。

PHP 操作 Redis 基本步骤如下：

1. 实例化 Redis 对象 `$redis = new Redis();`；
2. 连接 Redis 服务器 `$redis->connect('127.0.0.1', 6379);`；
3. 设置 Redis 模式；
4. 进行基本的操作；

```php
//创建redis实例
$redis = new Redis();
//连接redis实例，有以下几种方式
$redis->connect('127.0.0.1', 6379);
$redis->connect('127.0.0.1'); // port 6379 by default
$redis->connect('127.0.0.1', 6379, 2.5); // 2.5 sec timeout.
$redis->connect('/tmp/redis.sock'); // unix domain socket.
$redis->connect('127.0.0.1', 6379, 1, NULL, 100); // 1 sec timeout, 100ms delay between reconnection attempts.
//如果设置密码，则使用以下方法进行密码认证
$redis->auth('foobared');
```

#### 返回类型

```php
Redis::REDIS_STRING - String
Redis::REDIS_SET - Set
Redis::REDIS_LIST - List
Redis::REDIS_ZSET - Sorted set
Redis::REDIS_HASH - Hash
Redis::REDIS_NOT_FOUND - Not found / other
```

### string 操作

> Strings 数据结构是简单的key-value类型，value其实不仅是String，也可以是数字，除了一般的set,get之外还有以下功能：
> - 获取字符串长度
> - 往字符串append内容
> - 设置和获取字符串的某一段内容
> - 设置及获取字符串的某一位（bit）
> - 批量设置一系列字符串的内容


string 操作用到的命令有:
`getSet` `append` `getRange` `setRange` `strlen` `getBit/setBit` `incr, incrBy` `decr, decrBy` `get` `set` `setex` `setnx` `delete` `mset` `mget`

#### 使用场景

> 1. 计数
>

#### Example-1

```php
<?php
 $redis= new Redis();
 $redis->connect('127.0.0.1',6379);
 $redis->set('key','string value');
 $redis->set('test','123');
 $result = $redis->getMultiple(['key','test']);
 echo "<pre>";
 var_dump($redis->incr('test'));
 var_dump($redis->decr('test'));
 print_r($result);

```

### list 操作

> Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，Redis内部的很多实现，包括发送缓冲队列等也都是用的这个数据结构。
> 用Lists的PUSH操作，将任务存在Lists中，然后工作线程再用POP操作将任务取出进行执行。Redis还提供了操作Lists中某一段的api，你可以直接查询，删除Lists中某一段的元素。

list 操作用到的命令有(以`l`开头)：

`lPush/rPush` `lPushx/rPushx` `lPop/rPop` `blPop/brPop` `lSize` `lIndex, lGet` `lSet` `lRange, lGetRange` `lTrim, listTrim` `lRem, lRemove` `lInsert` `rpoplpush`

#### 使用场景

> 1. 显示最新的项目列表
> 2. 删除与过滤

#### Example-2

```php
<?php
 $redis= new Redis();
 $redis->connect('127.0.0.1',6379);
 $redis->delete('test');
 $redis->lpush('test','111');
 $redis->lpush('test','222');
 $redis->rpush('test','333');
 $redis->rpush('test','444');
 var_dump($redis->lpop('test')); //string(3) "222"
 var_dump($redis->rpop('test')); //string(3) "444"
 var_dump($redis->lsize('test')); //int(2)
 var_dump($redis->lset('test',1,'555')); //bool(true) "555"
 var_dump($redis->lget('test',1)); //string(3)
 var_dump($redis->lgetRange('test',0,-1));

```

### set 操作

> Set 集合的概念就是一堆**不重复值的组合**。
> 当你需要存储一个列表数据，又不希望出现重复数据时，set是一个很好的选择，并且set提供了判断某个成员是否在一个set集合内的重要接口，这个也是list所不能提供的。
> Redis 还为集合提供了求交集、并集、差集等操作，可以非常方便的实现如共同关注、共同喜好、二度好友等功能。

set 操作用到的命令有(以`s`开头)：
`sAdd` `sRem, sRemove` `sMove` `sIsMember, sContains` `sCard, sSize` `sPop` `sRandMember` `sInter` `sInterStore` `sUnion` `sUnionStore` `sDiff` `sDiffStore`  `sMembers, sGetMembers`

#### 使用场景

> 1. 特定时间内的特定项目
> 2. 删除与过滤

#### Example-3

```php
<?php
 $redis= new Redis();
 $redis->connect('127.0.0.1',6379);
 $redis->delete('test');
 $redis->delete('test1');
 $redis->sadd('test',111);
 $redis->sadd('test',333);
 $redis->sadd('test',222);
 $redis->sadd('test1',444);
 $redis->sadd('test1',555);
 $redis->sremove('test',111);
 $redis->smove('test','test1',333);
 echo "<pre>";
 var_dump($redis->sort('test1'));
 var_dump($redis->scontains('test1',555)); //检查集合中是否存在指定的值
 echo $redis->ssize('test1'); //返回集合中存储值的数量
 var_dump($redis->sinter('test','test1')); //返回一个所有指定键的交集。
 var_dump($redis->sdiffstore('new','test','test1')); //执行sdiff命令并把结果储存到新建的变量中。
 print_r($redis->smembers('new')); //返回集合的内容

```

### zset (sorted set)操作

> Redis sorted set的使用场景与set类似，区别是set不是自动有序的，而sorted set可以通过用户额外提供一个优先级(score)的参数来为成员排序，并且是插入有序的，即自动排序。当你需要一个有序的并且不重复的集合列表，那么可以选择sorted set数据结构。

zset 操作用到的命令有(以`z`开头)：
`zAdd` `zDelete, zRem` `zRange` `zRevRange` `zRangeByScore, zRevRangeByScore` `zCount` `zRemRangeByScore, zDeleteRangeByScore` `zSize, zCard` `zScore` `zRank, zRevRank` `zIncrBy` `zUnion/zInter`

#### 使用场景

> 1. 排行榜相关
> 2. 按照用户投票和时间排序
> 3. 处理过期项目
> 4. 社交关系

#### Example-4

```php
<?php
 $redis= new Redis();
 $redis->connect('127.0.0.1',6379);
 $redis->zadd('zset',1,'b'); //向有序集合key中添加元素value，score用于排序。
 $redis->zadd('zset',0,'a');
 $redis->zadd('zset',2,'c');
 $redis->zAdd('zset', 4, 'R');
 $redis->zAdd('zset', 5, 'd');
 $redis->zdelete('zset','c'); // 删除有序集合key中的元素value
 print_r($redis->zrange('zset',0,-1)); // 返回有序集合key中的index从start到end的所有元素【升序】
 print_r($redis->zcount('zset',0,3)); // 返回有序集合key中，index从start到end间的【包括start和end】所有元素的个数。
 $redis->zIncrBy('zset', 2, 'R'); // 将有序集合key中的元素value的score增加increment,不存在则创建
 print_r($redis->zScore('zset','R')); // 返回有序集合key中元素value的score
```

### hash 操作

> Hash结构可以使你像在数据库中Update一个属性一样只修改某一项属性值。
> Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现:
> - 这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap;
> - 当成员数量增大时会自动转成真正的HashMap,此时encoding为ht。

hash 操作用到的命令有(以`h`开头)：
`hSet` `hGet` `hLen` `hDel` `hKeys` `hVals` `hGetAll` `hIncrBy` `hExists` `hMset` `hMget`

#### 使用场景

> 1. 消息通知

#### Example-5

```php
<?php
 $redis= new Redis();
 $redis->connect('127.0.0.1',6379);
 $redis->hset('hash','lang1','php'); // 向哈希表hash中添加key为key，值为value的元素。
 $redis->hset('hash','lang2','mysql');
 $redis->hset('hash','lang3','mysql2');
 $redis->hset('hash','lang4','1');
 echo $redis->hget('hash','lang1');
 echo $redis->hlen('hash'); // 返回哈希表hash中元素的个数
 $redis->hdel('hash','lang3');
 print_r($redis->hvals('hash')); // 返回哈希表hash中所有的value
 print_r($redis->hgetall('hash')); // 返回哈希表hash中所有的元素
 var_dump( $redis->hExists('hash', 'lang1') ); // 检测哈希表hash中是否有元素key
 var_dump($redis->hincrby('hash','lang4',3)); // 哈希表hash中元素key的value加number
 $redis->hMSet('hash',['lang5'=>'java','lang6'=>'c']); // 批量向哈希表hash中添加元素。
 var_dump($redis->hMget('hash',['lang1','lang2'])); // 量获取哈希表hash中的元素。

```

### Redis 操作

- 事务模式： `multi, exec, discard` 分别是开启事务，执行事务，取消事务
- 清除数据库
 a. 清除当前数据库 `flushDB`
 b. 清除所有数据库 `flushAll`

- 随机返回key `randomKey`
- 选择一个数据库 `select`
- 转移一个 key 到另一个数据库 `move`
- 查看数据库有多少key `dbSize`
- 使用 AOF 进行数据持久化 `bgrewriteaof`
- 选择从服务器 `saveof`
- 数据保存到磁盘
 a. 同步保存到磁盘 `save`
 b. 异步保存到磁盘 `bgsave`
- 返回 Redis 的版本信息 `info`

### 友情链接

- [github phpredis](https://github.com/phpredis/phpredis)
- [php redis操作](http://www.cnblogs.com/weafer/archive/2011/09/21/2184059.html)
- [Redis 命令参考](http://redisdoc.com/)
- [Redis 和 Memcached的区别](http://blog.csdn.net/tonysz126/article/details/8280696/)
- [Redis应用场景](http://blog.csdn.net/hguisu/article/details/8836819)

---
