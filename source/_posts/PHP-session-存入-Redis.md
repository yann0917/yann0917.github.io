---
title: PHP session 存入 Redis
tags:
  - Redis
  - session

categories: Redis
abbrlink: 26648
date: 2017-05-01 00:01:19
---

### 原理

> 把 session 默认的存储方式由`file` 改为 `redis` 会提高速度，由于redis是在内存中读取的，其读取速度必然比在文件中读取速度快，同时解决了session共享的问题。

实现代码如下：

```php
<?php
header('Content-type:text/html;charset=utf-8');
// 配置session 存储方式
ini_set('session.save_handler', 'redis');
ini_set('session.save_path', 'tcp://127.0.0.1:6379');
//开启session
session_start();

$_SESSION['Redis_session'] = ['name'=>'yann','string'=>'hello redis'];
//连接 Redis
$redis= new Redis();
$redis->connect('127.0.0.1',6379);
echo 'session_id:'.session_id();
echo "<br>";
//redis用session_id作为key
echo $redis->get('PHPREDIS_SESSION:' . session_id());
```

---
