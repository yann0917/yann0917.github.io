---
title: file_get_contents抓取https出错解决办法
tags:
  - file_get_contents
  - Filesystem Functions
categories: php functions
abbrlink: 61369
date: 2017-05-06 19:43:07
---

### 出错内容

使用file_get_contents()获取`https://`url时出现以下错误：
`Warning: file_get_contents(): Unable to find the wrapper "https" - did you forget to enable it when you configured PHP`

### 解决办法

`file_get_contents — 将整个文件读入一个字符串`
`如果要打开有特殊字符的 URL （比如说有空格），就需要使用 urlencode() 进行 URL 编码`

#### 1. 开启openssl扩展

> 在php.ini 中开启`extension=php_openssl.dll`，并重启服务器即可。

#### 2. 使用curl函数禁止ssl

```php
<?php
//file_get_contents抓取https地址内容
function getCurl($url){
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL,$url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false); //阻止对证书的合法性的检查
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false); //不检查证书
$result = curl_exec($ch);
curl_close ($ch);
return $result;
}

```
