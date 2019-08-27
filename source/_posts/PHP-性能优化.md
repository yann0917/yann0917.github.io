---
title: PHP 性能优化
tags: PHP
categories: PHP
abbrlink: 10969
date: 2017-05-14 01:43:58
---

> 项目优化的时候，不要仅限于PHP 性能优化（占比30%~40%），还要注意其周边问题的性能优化。

### PHP 代码运行流程

![](http://opx5plj4u.bkt.clouddn.com/17-5-14/83626233-file_1494725692674_14427.png)

### 什么时候会出现性能问题？when

1. 语法使用不恰当；
2. 使用PHP 做它不擅长的事情；
3. 连接 PHP 的服务性能的问题；
4. PHP 自身短板；
5. 自己未知的问题；

### 优化方向

1. 语言级的性能优化，见效最快；
2. 周边问题的性能优化，如web server ，MySQL，缓存等；
3. php 语言自身分析和优化（底层代码分析）；

### 优化工具

安装：
> Linux OS 中任意目录下执行 `yum -y install httpd-tools`
> Windows OS 中 Apache bin 目录下自带 `ab.exe`

Apache Benchmark(ab)

```php
./ab -n100 -c100 url地址 请求数和并发数要设置的少一点以免对正常使用的网站造成实际的影响
//重点关注以下解析结果：
Requests per second (每一秒钟处理的请求数)优化后要尽可能多
Time per request(每一个请求处理的时间)优化后要尽可能小

```

用法举例：
> 执行 `ab -n100 -c100 http://www.baidu.com/`

```bash
[root@iZ2zedvndklojgb9e7bppqZ ~]# ab -n100 -c100 http://www.baidu.com/
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.baidu.com (be patient).....done


Server Software:        BWS/1.1
Server Hostname:        www.baidu.com
Server Port:            80

Document Path:          /
Document Length:        102087 bytes

Concurrency Level:      100
Time taken for tests:   0.185 seconds
Complete requests:      100
Failed requests:        98
   (Connect: 0, Receive: 0, Length: 98, Exceptions: 0)
Write errors:           0
Total transferred:      10306408 bytes
HTML transferred:       10211042 bytes
Requests per second:    539.96 [#/sec] (mean)
Time per request:       185.199 [ms] (mean)
Time per request:       1.852 [ms] (mean, across all concurrent requests)
Transfer rate:          54346.14 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        3    4   0.7      4       6
Processing:    31   35  19.5     33     172
Waiting:        4   20  21.2     19     165
Total:         36   40  19.3     37     175

Percentage of the requests served within a certain time (ms)
  50%     37
  66%     37
  75%     37
  80%     37
  90%     37
  95%     38
  98%    173
  99%    175
 100%    175 (longest request)
```

### 优化点 How

#### 语言级的性能优化

1. 少写代码降低代码冗余，多用 PHP 自身能力，多使用 PHP 内置变量，常量，函数；

   > php代码运行流程：zend引擎扫描.php文件，Scanning之后将PHP代码转换成语言片段(Tokens),parsing之后将Tokens解析成自己能识别的格式，再编译成opcode（最终执行的机器码），然后顺次执行，输出。

2. PHP 内置函数之间，依然存在快慢差异，多了解 PHP 内置函数的时间复杂度；

   > 相同条件下`isset（）`比`array_key_exists()`时间消耗更少

3. 尽量少用魔术函数，省事的同时，牺牲了 PHP 的性能；
4. 错误抑制符`@`会产生额外的开销；

   > 在代码开始前，结束后增加Opcode，忽略报错信息。
   > `vld扩展` 可以查看PHP Opcode，[linux下安装步骤](http://blog.csdn.net/21aspnet/article/details/7002644)，源码编译在php.ini中开启此扩展。
   > 执行 `php -dvld.active=1 -dvld.execute=0 xxx.php` 可查看分析结果

5. 合理使用内存，虽然 PHP 有内存回收机制，但是可以利用`unset()`及时释放不使用的内存(unset()会出现注销不掉的情况)；
6. 正则表达式性能比较低，如果能利用字符串函数完成的，尽量少使用正则表达式；
7. 避免在循环内做运算，如在循环判断式中被重复计算；
8. PHP 不适合做密集型业务：大批量的日志分析、大批量数据处理、处理大数据；
9. 使用带引号的字符串当作键值，PHP 会将没有引号的键值当作常量，产生查找常量的开销；

#### 周边问题的性能优化

1. 服务器性能会影响PHP 的执行速度；
2. 文件存储，硬件，数据库，缓存，网络等周边依赖决定了PHP 的运行速度；
3. 找出问题的核心，抓大头去优化；
4. 减少文件类的操作，开销如下：读写内存< 读写数据库< 读写磁盘< 读写网络数据(socket通信)；
5. 优化网络请求
   > 1. 设置超时请求：连接超时不超过200ms,读超时不超过800ms,写超时不超过500ms；
   > 2. 将串行请求并行化：
   >> a.使用`curl_multi_*()`系列函数，注意有一个请求慢了，整个请求就变慢了；
   >> b.使用swoole扩展；

6. 压缩 PHP 接口输出压缩，使用gzip即可，加快数据传输速度，但是会产生额外的CPU开销(100k以上的数据效果更明显)；
7. 缓存复用，如smarty 模板缓存。

### PHP 性能瓶颈究极方案

> Opcode 做缓存，使用 APC扩展，（已停止维护），PHP 5.5.0 及后续版本中已经绑定了 OPcache 扩展。
> 使用 PHP 扩展代替原生代码中的高频逻辑；
> Runtime 优化 ，HHVM；

### 性能问题分析工具

> xhprof 来自facebook的php性能分析工具，先做性能分析，再进行优化。php --ri xhprof,检查当前服务器扩展是否支持xhprof,如果输出xprof的版本，说明可以支持。
> xhprof_enable();开启xhprof,通过xhprof_disable()返回的性能数据,将性能数据保存在目录中。
> 注：此工具在PHP7 上无效，建议使用tideways。[PHP性能被动分析工具之xhgui加tideways的安装实践](https://segmentfault.com/a/1190000007580819)

### 附录

#### vld 扩展的使用举例

good.php文件内容如下，其中包含错误抑制符`@`。

```php
<?php
    @file_get_contents('xxx');//xxx文件不存在
```

执行 `php -dvld.active=1 -dvld.execute=0 good.php` 可查看Opcode，在错误抑制符代码前后新增了`BEGIN_SILENC`，`END_SILENCE`

```bash
[root@iZ2zedvndklojgb9e7bppqZ default]# php -dvld.active=1 -dvld.execute=0 good.php
Finding entry points
Branch analysis from position: 0
Jump found. Position 1 = -2
filename:       /home/wwwroot/default/good.php
function name:  (null)
number of ops:  5
compiled vars:  none
line     #* E I O op                           fetch          ext  return  operands
-------------------------------------------------------------------------------------
   2     0  E >   BEGIN_SILENCE                                    ~0
         1        SEND_VAL                                                 'xxx'
         2        DO_FCALL                                      1          'file_get_contents'
         3        END_SILENCE                                              ~0
   3     4      > RETURN                                                   1

branch: #  0; line:     2-    3; sop:     0; eop:     4; out1:  -2
path #1: 0,
```

#### XHProf 使用简介

1. [使用XHProf查找PHP性能瓶颈](https://segmentfault.com/a/1190000003509917)
2. [深入理解PHP Opcode缓存原理](https://blog.linuxeye.cn/361.html)

---
