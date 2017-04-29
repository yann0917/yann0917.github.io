---
title: curl 函数
date: 2017-04-29 23:16:48
tags: php
categories: php 函数库
---
###  curl函数
PHP 支持` libcurl 库`，能够连接通讯各种服务器、使用各种协议。libcurl 目前支持的协议有` http、https、ftp、gopher、telnet、dict、file、ldap`。 libcurl 同时支持` HTTPS 证书`、`HTTP POST`、`HTTP PUT`、 `FTP 上传`(也能通过 PHP 的 FTP 扩展完成)、`HTTP 基于表单的上传`、`代理`、`cookies`、`用户名+密码的认证`。 

### 原理
> 使用 cURL 函数的基本步骤:
> 1. 先使用` curl_init() `初始化 cURL会话
> 2. 接着可以通过` curl_setopt() `设置需要的传输选项，可设置POST提交方式
> 3. 然后使用` curl_exec() `来执行会话，
> 4. 当执行完会话后使用` curl_close() `关闭会话。

### 使用场景
> 1. 接口编程中获取接口API；
> 2. 使用此函数封装成一个类，以POST或GET方式请求微信提供的url,如获取微信的ACCESS_TOKEN；
> 3. 发送POST表单；
> 4. 上传文件,PHP5.5之后建议使用`CURLFile()`对象上传文件；
> 5. 其他常见的操作...

### 实例
```php
<?php
  //example-1:微信POST请求
  function http_request($url, $data = null){
   $ch = curl_init();
    $option = [
        CURLOPT_URL=>$url,
        CURLOPT_SSL_VERIFYHOST=>false,
        CURLOPT_SSL_VERIFYPEER=>false,
        CURLOPT_RETURNTRANSFER=>true,
    ];

    if(!empty($data)) {
        $option += [
            CURLOPT_POST=>true,
            CURLOPT_POSTFIELDS=>$data
        ];
    }
    curl_setopt_array($ch,$option);

    $output = curl_exec($ch);
    curl_close($ch);
    return $output;
}

//example-2:获取网页内容
$ch = curl_init("http://www.example.com/");
$fp = fopen("example_homepage.txt", "w");

curl_setopt($ch, CURLOPT_FILE, $fp);
curl_setopt($ch, CURLOPT_HEADER, 0);

curl_exec($ch);
curl_close($ch);
fclose($fp);
?> 
```
---