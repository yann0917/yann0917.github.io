---
title: PHP 操作 Memcache
date: 2017-05-03 00:36:26
tags: 
 - Memcache
 - PHP


categories: Memcache
---

### PHP 操作 Memcache
#### Memcache 简介
Memcache 是高速分布式的内存对象缓存系统，用于动态Web应用以减轻数据库的负载。它通过在内存中缓存数据和对象来减少读取数据库的次数，从而提高了网站访问的速度。
#### Memcache 特性
 - MemCache虽然被称为"分布式缓存"，但是**MemCache本身完全不具备分布式的功能**，MemCache集群之间不会相互通信，所谓的"分布式"，完全依赖于客户端程序的实现。
 - 其数据保存在内存中，所以服务器宕机或者重启数据都会失效；
 - Memcache中可以保存的item数量没有限制，只要内存足够；
 - Memcache单进程在32bit机上最大使用内存为2G,64bit机无限制；
 - key最大为250byte，超过该长度则无法保存；
 - 单个item最大数据是1M，超过1M的数据不能存储；
 - 不能遍历所有的item，这个操作的速度相对缓慢且会阻塞其他操作；
 - Memcache设置添加一个key的时候，传入expire为0表示永不过期，设置了过期时间，不能超过30天，这个key会在30天之后失效；
 - Memcache的高性能源自于两阶段哈希结构：第一阶段在客户端，通过Hash算法根据Key值算出一个节点；第二阶段在服务端，通过一个内部的Hash算法，查找真正的item并返回给客户端。从实现的角度看，MemCache是一个非阻塞的、基于事件的服务器程序。


### PHP 操作Memcache 类
创建memcache实例，然后连接至memcache服务器即可使用memcache，默认端口是`11211`;

```php
$memcache = new Memcache;
$memcache->connect('127.0.0.1', 11211);
$memcache->pconnec('127.0.0.1', 11211); 
// 打开一个到服务器的持久化连接 , 连接不会在脚本执行结束后或者close()被调用后关闭
$memcache->addServer('123.57.210.55', 11211,$persistent,$weight); 
// 向连接池中添加一个memcache服务器 $persistent 是否持久化连接 
// $weight 控制桶的数量控制被选中的权重 $timeout 表示连续持续时间
$memcache->set('name', 'TK'); 
// 默认存储不压缩 不过期 , 其中字符串和数值直接存储，其他类型序列化后存储 
// set其实是add方法和replace方法集合
$memcache->set('email', 'yann09@163.com',MEMCACHE_COMPRESSED,5);
// MEMCACHE_COMPRESSED设置存储是否压缩 , 5表示5秒后过期但是最大只能设置2592000秒(30天)
// 如果设置为0 表示永不过期, 可以设置将来的时间戳
$memcache->set('info',array('age'=>'26','salary'=>'1000'));  
// 可以直接存储数组,redis中存储需要手动serialize()序列化
$memcache->add('counter', '10', MEMCACHE_COMPRESSED, 0); 
// 如果键值存在会返回false , 如果不存在, 和set方法一样，生成一个counter的key并赋值10
$memcache->replace ('counter', '10');
// 如果键值不存在会返回false , 如果存在, 替换counter的值为10
$memcache->increment('counter', 3); 
// 首先将元素当前值转换成数值然后加上value 操作counter键值+3 
// 若键不存在 则返回false 不能用于压缩的键值操作，否则get键会失败
$memcache->decrement('counter', 3); // 操作counter键值-3, 若键不存在 则返回false
$memcache->delete('counter', 3); // 操作删除键counter, 3表示3秒内删除，默认是0即立即删除
$memcache->flush(); 
// 立即使所有已经存在的元素失效, 并不会真正的释放任何资源，
// 而是仅仅标记所有元素都失效了，因此已经被使用的内存会被新的元素复写。
$memcache->getExtendedStats (); 
// 返回一个二维关联数据的服务器统计信息。数组的key由host:port方式组成
$memcache->getServerStatus ('127.0.0.1'); // 获取返回一个服务器的在线/离线状态  0表示离线 非0在线
$memcache->getStats(); // 获取服务器统计信息
$memcache->getVersion(); // 返回服务器版本信息
$memcache->setCompressThreshold ($threshold, $min_saving); 
//  开启大值自动压缩   $threshold设置压缩阀值 2000字节 ，即字节数大于2K 就压缩
//  $min_saving  0--1之间  0.2表示压缩20%
$memcache->setServerParams('memcache_host', 11211, 1, 15, true, '_callback_memcache_failure'); 
// $memcache->addServer('memcache_host', 11211, false, 1, 1, -1, false);
// 已经通过addServer 配置过服务器 使用setServerParams 重新设置配置信息

```

### 友情链接
- [MemCache详细解读](http://www.cnblogs.com/xrq730/p/4948707.html)

---