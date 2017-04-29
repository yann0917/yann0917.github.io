---
title: ob 函数
date: 2017-04-29 23:14:37
tags: php
categories: php 函数库
---
### ob函数 ---【页面静态化】
当PHP脚本有输出时，输出控制函数可以用这些来控制输出。这在多种不同情况中非常有用，尤其是用来**在脚本开始输出数据后，发送http头信息到浏览器**。输出控制函数不影响由 header() 或 setcookie()发送的文件头信息，仅影响像 echo这样的函数和PHP代码块间的数据。

### 原理
> 1. 在php.ini 中开启 output_buffering=4096;或者在程序运行时 `ob_start()`,此后可以使用ob其他函数
> 2. 如果想进一步处理缓冲区内容的话，可以在 `ob_flush()` 之前调用 `ob_get_contents()` ;
 >>`ob_flush()` 冲刷出 PHP 输出缓冲区中的内容，调用此函数之后缓冲区内容会被丢弃；（`ob_end_flush()` 函数会销毁缓冲区）
 >>`flush()` 该函数将当前为止程序的所有输出发送到用户的浏览器，最好在`ob_flush()`之后使用此函数。
> 3. `ob_end_clean()`清空（擦除）缓冲区并关闭输出缓冲。
> 4. 使用` file_put_contents() `将缓冲区内容写入到文件中，此时的文件就是静态化的。


### 使用场景
> 1. 页面静态化 ，将缓冲区的内容接收到本地文件中，然后读取本地文件，提高速率。

### 实例

```php
<?php

    ob_start(); //打开缓冲区   
    phpinfo(); //使用phpinfo函数   
    $info=ob_get_contents(); //得到缓冲区的内容并且赋值给$info   
    $file=fopen('info.txt','w'); //打开文件info.txt   
    fwrite($file,$info); //写入信息到info.txt   
    fclose($file); //关闭文件info.txt  

```
---