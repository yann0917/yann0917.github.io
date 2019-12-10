---
title: Redis 配置文件详解
tags: Redis
categories: Redis
abbrlink: 65309
date: 2017-05-02 12:29:29
---

## redis.conf配置文件

[redis.conf 下载地址](http://download.redis.io/redis-stable/redis.conf)

> redis.conf 主要配置项有：
>
> 1. 文件包含 [包含其他配置文件]
> 2. 常用 [守护进程，TCP，监听客户端IP,日志文件]
> 3. 快照 [RDB 持久化]
> 4. 主从复制
> 5. 安全
> 6. 限制
> 7. aof 模式[aof 持久化]
> 8. LUA scripting
> 9. Redis 集群
> 10. slow log
> 11. 事件通知
> 12. 高级配置项

```txt
daemonize yes #是否以后台进程运行，默认为no
pidfile /var/run/redis.pid #如以后台进程运行，则需指定一个pid，默认为/var/run/redis.pid
bind 127.0.0.1 #绑定主机IP，默认值为127.0.0.1
port 6379 #监听端口，默认为6379
timeout 300 #超时时间，默认为300（秒）
loglevel notice #日志记slave-serve-stale-data yes：在master服务器挂掉或者同步失败时，从服务器是否继续提供服务。录等级，有4个可选值，debug，verbose（默认值），notice，warning
logfile /var/log/redis.log #日志记录方式，默认值为stdout
databases 16 #可用数据库数，默认值为16，默认数据库为0

save 900 1 #900秒（15分钟）内至少有1个key被改变
save 300 10 #300秒（5分钟）内至少有300个key被改变
save 60 10000 #60秒内至少有10000个key被改变
rdbcompression yes #存储至本地数据库时是否压缩数据，默认为yes
dbfilename dump.rdb #本地数据库文件名，默认值为dump.rdb
dir ./ #本地数据库存放路径，默认值为 ./

slaveof 10.0.0.12 6379 #当本机为从服务时，设置主服务的IP及端口
masterauth elain #当本机为从服务时，设置主服务的连接密码
slave-serve-stale-data yes #在master服务器挂掉或者同步失败时，从服务器是否继续提供服务。
requirepass elain #连接密码

maxclients 128 #最大客户端连接数，默认不限制
maxmemory #设置最大内存，达到最大内存设置后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理后，任到达最大内存设置，将无法再进行写入操作。
appendonly no #是否在每次更新操作后进行日志记录，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认值为no
appendfilename appendonly.aof #更新日志文件名，默认值为appendonly.aof
appendfsync everysec #更新日志条件，共有3个可选值。no表示等操作系统进行数据缓存同步到磁盘，always表示每次更新操作后手动调用fsync()将数据写到磁盘，everysec表示每秒同步一次（默认值）。

really-use-vm yes
vm-enabled yes #是否使用虚拟内存，默认值为no
vm-swap-file /tmp/redis.swap #虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
vm-max-memory 0 #vm大小限制。0：不限制，建议60-80% 可用内存大小。
vm-page-size 32 #根据缓存内容大小调整，默认32字节。
vm-pages 134217728 #page数。每 8 page，会占用1字节内存。
vm-page-size #vm-pages 等于 swap 文件大小
vm-max-threads 4 #vm 最大io线程数。注意： 0 标志禁止使用vm
hash-max-zipmap-entries 512
hash-max-zipmap-value 64

list-max-ziplist-entries 512
list-max-ziplist-value 64
set-max-intset-entries 512
activerehashing yes
```

### 友情链接

- [Redis.conf配置详细解析](http://www.cnblogs.com/kreo/p/4423362.html)

---
