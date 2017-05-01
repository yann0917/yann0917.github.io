---
title: PHP基础-标记
categories: php
tags: php
abbrlink: 33536
date: 2017-04-28 14:32:55
---

> 测试环境
> PHP version 5.3.29/5.6.29/7.1.0
> Apache 2.4.25

### PHP标记的作用

- 当解析一个文件时，PHP 会寻找起始和结束标记，也就是 <?php 和 ?>，这告诉 PHP 开始和停止解析二者之间的代码。此种解析方式使得 PHP 可以被嵌入到各种不同的文档中去，而任何起始和结束标记之外的部分都会被 PHP 解析器忽略。 

<!-- more -->

- PHP 也允许使用短标记 <? 和 ?>，但不鼓励使用。只有通过激活 php.ini 中的 short_open_tag 配置指令或者在编译 PHP 时使用了配置选项 --enable-short-tags 时才能使用短标记。 


- **如果文件内容是纯 PHP 代码，最好在文件末尾删除 PHP 结束标记。这可以避免在 PHP 结束标记之后万一意外加入了空格或者换行符，会导致 PHP 开始输出这些空白，而脚本中此时并无输出的意图。**

### 变更日志

> 自 PHP 7.0.0 起，已经移除 ASP 标记 `<%, %>, <%=`和 script标记 `<script language="php" >` ;
> 自 PHP 5.4 起，短格式的 echo 标记 `<?=` 总会被合法识别，而不管 short_open_tag 设置的是on还是off。


### 开始标记`<?php` 后应该有空白

> 这里的空白指的是换行、回车、tab、空格。

#### Example-1

> `<?php` 标签后没有空白echo 不起作用:


```php
<?php/*blah*/ echo "a"?> 

```

以下四种空白方式都起作用:

```php
1. <?php /*php 后面有空白[space]*/ echo "a"?>
```

```php
2. <?php
/*php 后面是行结束*/ echo "a"?>
```
```php
3. <?php    /*php后面是 tab*/ echo "a"?>

```

```php
4. <?php 
/*php 后面是空格然后是行结束*/ echo "a"?>

```



### 标记可分离

> 标记分离适用于PHP和HTML混编的代码中

#### Example-2
```php
<?php for ($i = 0; $i < 5; ++$i): ?>
Hello, there!
<?php endfor; ?>

```

### 结束标记`?>`在`//`单行注释中

> 结束标记`?>`在//单行注释中会退出php模式，并返回html模式

#### Example-3
```php
<?php
//  $file_contents  = '<?php die(); ?>' . "\n";
 ?>

//会输出' . "\n"; ?>
//解决办法是使用 /**/块注释，或者使用字符串拼接标记
<?php
   $file_contents  = '<' . '?php die(); ?' . '>' . "\n";
?> 
```

----------
