---
title: PHP 操作 MySQL
date: 2017-04-29 23:19:47
tags: MySQL
categories: php 函数库
---
###  PHP 操作 MySQL
> PHP 5.5.0 起已废弃，并在自 PHP 7.0.0 开始被移除。应使用 MySQLi 或 PDO_MySQL 扩展来替换。

基本的连接步骤如下：
> 1. 连接数据库 `mysql_connect()`
> 2. 设置字符集 `mysql_set_charset('utf8')`  `mysql_query('set names utf8')`
> 3. 选择数据库 `mysql_select_db('db_name')`  `mysql_query('use db_name')`
> 4. 执行查询语句 `mysql_query()`
> 5. 打印结果集 `mysql_fetch_array()` `mysql_fetch_row`
> 6. 释放结果集 `mysql_free_result($result)`
> 7. 关闭连接 `mysql_close($link)`


```php
<?php
// 连接、选择数据库
$link = mysql_connect('mysql_host', 'mysql_user', 'mysql_password')
    or die('Could not connect: ' . mysql_error());
echo 'Connected successfully';
mysql_select_db('my_database') or die('Could not select database');

// 执行 SQL 查询
$query = 'SELECT * FROM my_table';
$result = mysql_query($query) or die('Query failed: ' . mysql_error());

// 以 HTML 打印查询结果
echo "<table>\n";
while ($line = mysql_fetch_array($result, MYSQL_ASSOC)) {
    echo "\t<tr>\n";
    foreach ($line as $col_value) {
        echo "\t\t<td>$col_value</td>\n";
    }
    echo "\t</tr>\n";
}
echo "</table>\n";

// 释放结果集
mysql_free_result($result);

// 关闭连接
mysql_close($link);
?> 
```
---