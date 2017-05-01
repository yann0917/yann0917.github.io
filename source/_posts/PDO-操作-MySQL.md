---
title: PDO 操作 MySQL
tags:
  - MySQL
  - php
categories: php 函数库
abbrlink: 58420
date: 2017-04-29 23:39:48
---

PHP 数据对象 （PDO） 扩展为PHP访问数据库定义了一个轻量级的一致接口。实现 PDO 接口的每个数据库驱动可以公开具体数据库的特性作为标准扩展功能。
PDO 提供了一个 **数据访问** 抽象层，这意味着，不管使用哪种数据库，都可以用相同的函数（方法）来查询和获取数据。 PDO 不提供 **数据库** 抽象层；
PDO常用方法有：

1. 预处理语句

 ```
 a. 执行预处理：$stmt = $pdo->prepare($sql);  
   //sql语句中的参数使用？占位符或者 :para 命名参数进行占位
 b. 设置参数$stmt->bindValue($parameter,$value);
   //注意使用？和：val占位符方式，此函数的参数标识符不同
 c. 执行预处理语句：$stmt->execute();

 ```

2. 事务处理

 ```
 a. 开启事务：$pdo->beginTransaction();
 b. 提交事务：$pdo->commit();
 c. 回滚事务：$pdo->rollBack();

 ```

3. 错误处理 `try catch`语句



### 原理
使用PDO需要PHP开启PDO扩展，使用步骤如下：
> 1. 在php.ini中开启`extension=php_pdo_mysql.dll`；
> 2. 设置dsn ，`$dsn="mysql:host=localhost;port=3306;dbname=test_db";`
> 3. 设置字符集，`$opt=array(PDO::MYSQL_ATTR_INIT_COMMAND=>'set names utf8');`
> 4. 实例化PDO对象，`$pdo = new PDO($dsn,$user_name,$pwd,$opt);`返回的是PDO对象
> 5. 执行语句，查询使用`$pdo->query()`返回的是结果集对象`PDOStatement`或false，增删改使用`$pdo->exec()`返回的是true or false;
> 6. 处理结果集对象`$stmt->fetch(PDO::FETCH_ASSOC)`或`$stmt->fetchAll()`
> 7. 关闭结果集`$stmt->closeCursor()`; 
> 8. 销毁PDO对象：`$pdo = null;` 


### 场景
1. 预处理语句可以防SQL注入,“重复执行”多条结构类似的sql语句，避免重复分析/编译/优化周期；
2. 不同数据库使用相同的方法名，解决数据库连接不统一的问题。


### 实例
```php
<?php
  $sql="select user_id,user_name,age,edu from user_list where user_id=:v1 and user_name=:v2";
  $stmt=$pdo->prepare($sql);
  $stmt->bindValue(":v1",6);   //占位符按自然顺序排,从1开始,user_id=6
  $stmt->bindValue(":v2",'user2');
  $stmt->execute();
  $arr=$stmt->fetch(PDO::FETCH_ASSOC);
  print_r($arr);
```