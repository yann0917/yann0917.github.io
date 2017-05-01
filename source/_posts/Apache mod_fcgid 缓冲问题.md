---
title: Apache mod_fcgid 缓冲问题
tags: php
categories: Apache
abbrlink: 58063
date: 2017-04-29 22:40:24
---

> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache/2.4.25 (Win32) mod_fcgid/2.3.9

### 问题描述
> Apache使用mod_fcgid 配置php出现的问题：在php.ini中配置output_buffering = Off或者output_buffering = 4096 均不能使用ob_flush()和flush()刷出缓冲区内容
	
	在php.ini中设置缓冲区
	output_buffering = 4096 ;开启php缓冲区
	output_buffering = Off ;关闭php缓冲区
	

### 问题分析

1. 在不同版本的php.ini中均关闭缓冲区，测试发现全都不生效，排除php.ini配置文件的问题；
2. 由于三个版本的php是由mod_fcgid配置的，初步定位是mod_fcgid配置问题。


### 解决办法

1. 查找官方文档发现，FcgidOutputBufferSize配置信息如下：[mod_fcgid document](http://httpd.apache.org/mod_fcgid/mod/mod_fcgid.html)
	```
    Description:	CGI output buffer size
    Syntax:		FcgidOutputBufferSize bytes
    Default:	FcgidOutputBufferSize 65536
    Context:	server config, virtual host
    Status:		External
    Module:		mod_fcgid
	```

2. Apache mod_fcgid模式默认为开启buffer，所以在Apache配置文件中设置 `FcgidOutputBufferSize 0` 
3. 重新测试代码，发现ob_flush()和flush()函数可用，即php.ini中配置缓冲区信息生效。

### 测试代码
> 测试要求在浏览器中间隔一秒出现一个数字：
> 一次php代码文件请求涉及两个缓冲区：
> 
> 1. php缓冲区：php编译生成的静态内容首先被php缓冲区收集，之后再对外输出;
> 2. browser缓冲区：从服务器过来的内容到达浏览器里边的时候，首先被浏览器缓冲区收集，收集满之后再逐渐展示到页面上;

```php
<?php
    //提前开启缓冲区，在php.ini中配置output_buffering = 4096;
    //提前开启缓冲区，或者在运行时设置ob_start();
    for($i=0;$i<5;$i++){
        echo $i,'<br/>';
        ob_flush(); //刷出php缓冲区内容
        flush(); //刷出浏览器缓冲区内容 
        sleep(1);
    }


```