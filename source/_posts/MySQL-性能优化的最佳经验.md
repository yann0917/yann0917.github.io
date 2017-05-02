---
title: MySQL 性能优化的最佳经验
tags:
  - MySQL
  - 优化
categories: MySQL
abbrlink: 62352
date: 2017-05-01 17:11:15
---

### 1. 为查询缓存优化你的查询
当有很多相同的查询被执行了多次的时候，这些查询结果会被放到一个缓存中，这样，后续的相同的查询就不用操作表而直接访问缓存结果了。
```php
# query cache does NOT work
$r = mysql_query("SELECT username FROM user WHERE signup_date >= CURDATE()");

# query cache works!
$today = date("Y-m-d");
$r = mysql_query("SELECT username FROM user WHERE signup_date >= '$today'");


```
以上两条SQL语句的差别就是 CURDATE() ，MySQL的查询缓存对这个函数不起作用。所以，像 NOW() 和 RAND() 或是其它的非确定的SQL函数都不会开启查询缓存，因为这些函数返回的结果是易变的。所以，我们需要做的就是，在php语句中用一个变量来代替MySQL的函数，从而开启缓存。


### 2. EXPLAIN 你的 SELECT 查询
使用` EXPLAIN `关键字可以让你知道MySQL是如何处理你的SQL语句的。这可以帮你分析你的查询语句或是表结构的性能瓶颈。

EXPLAIN 的查询结果还会告诉你哪个索引被利用(key列)，你的数据表是如何被搜索和排序的……查看rows列可以让我们找到潜在的性能问题。

### 3. 当只要一行数据时使用 LIMIT 1
当你查询表的时候，你已经知道结果只会有一条结果，但因为你可能需要去fetch游标，或是你也许会去检查返回的记录数。
在这种情况下，在查询语句上加上` LIMIT 1 `可以增加性能。这样，MySQL数据库引擎会在找到一条数据后停止搜索，而不是在整张表或索引上继续查找下一条符合记录的数据。


``` php
# do I have any users from Alabama?

# what NOT to do:
$r = mysql_query("SELECT * FROM user WHERE state = 'Alabama'");
if (mysql_num_rows($r) > 0) {
    # ...
}

# much better:
$r = mysql_query("SELECT 1 FROM user WHERE state = 'Alabama' LIMIT 1");
if (mysql_num_rows($r) > 0) {
    # ...
}

```

### 4. 为搜索字段建索引
索引并不一定就是给主键(primary key)或是唯一(unique key)的字段。如果在你的表中，有某个字段你总要会经常用来做搜索，那么，请为其建立索引吧。
![index](https://cdn.tutsplus.com/net/uploads/legacy/500_mysql/search_index.jpg)
你应该需要知道什么样的搜索是不能使用正常的索引的。索引使用需要符合字段独立，左原则，or原则。
### 5. 在Join表的时候索引和使用相同类型的列
如果你的应用程序有很多 JOIN 查询，你应该确认两个表中Join的字段是被建过索引的。这样，MySQL内部会启动优化Join操作的机制。

而且，这些被用来Join的字段，应该具有相同的类型。例如：如果你要把 DECIMAL 字段和一个 INT 字段Join在一起，MySQL就无法使用它们的索引。对于那些STRING类型，还需要有相同的字符集才行。（两个表的字符集有可能不一样）
```php
# looking for companies in my state
$r = mysql_query("SELECT company_name FROM users
    LEFT JOIN companies ON (users.state = companies.state)
    WHERE users.id = $user_id");
 
# both state columns should be indexed
# and they both should be the same type and character encoding
# or MySQL might do full table scans
```
### 6. 千万不要 ORDER BY RAND()

如果你真的想把返回的数据行打乱了，你有N种方法可以达到这个目的。这样使用只让你的数据库的性能呈指数级的下降。这里的问题是：MySQL会不得不去执行RAND()函数（很耗CPU时间），而且这是为了每一行记录去记行，然后再对其排序。就算是你用了Limit 1也无济于事（因为要排序）。
```php
# what NOT to do:
$r = mysql_query("SELECT username FROM user ORDER BY RAND() LIMIT 1");
 
# much better:
$r = mysql_query("SELECT count(*) FROM user");
$d = mysql_fetch_row($r);
$rand = mt_rand(0,$d[0] - 1);
 
$r = mysql_query("SELECT username FROM user LIMIT $rand, 1");
```
### 7. 避免 SELECT *
从数据库里读出越多的数据，那么查询就会变得越慢，增加了磁盘操作的时间。并且，如果你的数据库服务器和WEB服务器是两台独立的服务器的话，这还会增加网络传输的负载。
所以，你应该养成一个需要什么就取什么的好的习惯。
```php
# not preferred
$r = mysql_query("SELECT * FROM user WHERE user_id = 1");
$d = mysql_fetch_assoc($r);
echo "Welcome {$d['username']}";
 
# better:
$r = mysql_query("SELECT username FROM user WHERE user_id = 1");
$d = mysql_fetch_assoc($r);
echo "Welcome {$d['username']}";
 

```

### 8. 永远为每张表设置一个ID

我们应该为数据库里的每张表都设置一个ID做为其主键，而且最好的是一个INT型的（推荐使用UNSIGNED），并设置上自动增加的AUTO_INCREMENT标志。

就算是你 users 表有一个主键叫 “username”的唯一字段，你也别让它成为主键。使用 VARCHAR 类型来当主键会使用得性能下降。另外，在你的程序中，你应该使用表的ID来构造你的数据结构。

而且，在MySQL数据引擎下，还有一些操作需要使用主键，在这些情况下，主键的性能和设置变得非常重要，比如，集群，分区……

在这里，只有一个情况是例外，那就是“关联表”的“外键”，也就是说，这个表的主键，通过若干个其他表的主键构成。我们把这个情况叫做“外键”。比如：有一个“学生表”有学生的ID，有一个“课程表”有课程ID，那么，“成绩表”就是“关联表”了，其关联了学生表和课程表，在成绩表中，学生ID和课程ID叫“外键”其共同组成主键。

### 9. 使用 ENUM 而不是 VARCHAR

ENUM 类型是非常快和紧凑的。在数据库内部，像TINYINT类型一样保存，但其外表上显示为字符串。这样一来，用这个字段来做一些选项列表变得相当的完美。

如果你有一个字段，这些字段的取值是有限而且固定的，那么，你应该使用 ENUM 而不是 VARCHAR。比如有个列名叫做“状态”，其值为“活动”，“非活动”，“搁置”，“过期”等。


### 10. 从 PROCEDURE ANALYSE() 取得建议

PROCEDURE ANALYSE() 会让 MySQL 帮你去分析你的字段和其实际的数据，并会给你一些有用的建议。只有表中有实际的数据，这些建议才会变得有用，因为要做一些大的决定是需要有数据作为基础的。

例如，如果你创建了一个 INT 字段作为你的主键，然而并没有太多的数据，那么，PROCEDURE ANALYSE()会建议你把这个字段的类型改成 MEDIUMINT 。或是你使用了一个 VARCHAR 字段，因为数据不多，你可能会得到一个让你把它改成 ENUM 的建议。这些建议，都是可能因为数据不够多，所以决策做得就不够准。

> PROCEDURE ANALYSE() is deprecated as of MySQL 5.7.18, and is removed in MySQL 8.0.

### 11. 尽可能的使用 NOT NULL

除非你有一个很特别的原因去使用 NULL 值，你应该总是让你的字段保持 NOT NULL。

首先，问问你自己“Empty”和“NULL”有多大的区别（如果是INT，那就是0和NULL）？如果你觉得它们之间没有什么区别，那么你就不要使用NULL。（你知道吗？在 Oracle 里，NULL 和 Empty 的字符串是一样的！)

NULL 需要额外的空间，并且，在你进行比较的时候，你的程序会更复杂。 当然，这里并不是说你就不能使用NULL了，现实情况是很复杂的，依然会有些情况下，你需要使用NULL值。



### 12. Prepared Statements
Prepared Statements很像存储过程，是一种运行在后台的SQL语句集合，我们可以从使用 prepared statements 获得很多好处，无论是性能问题还是安全问题。

Prepared Statements 可以检查一些你绑定好的变量，这样可以保护你的程序不会受到“SQL注入式”攻击。当然，你也可以手动地检查你的这些变量，然而，手动的检查容易出问题，而且很经常会被程序员忘了。当我们使用一些framework或是ORM的时候，这样的问题会好一些。

在性能方面，当一个相同的查询被使用多次的时候，这会为你带来可观的性能优势。你可以给这些Prepared Statements定义一些参数，而MySQL只会解析一次。

最新版本的MySQL在传输Prepared Statements是使用二进制形式，这会使得网络传输非常有效率。

当然，也有一些情况下，我们需要避免使用Prepared Statements，因为其不支持查询缓存。但据说版本5.1后支持了。

在PHP中要使用prepared statements，你可以查看其使用手册：[mysqli](http://php.net/manual/zh/book.mysqli.php)扩展 或是使用数据库抽象层，如： [PDO](http://us.php.net/manual/zh/book.pdo.php).
```php
# create a prepared statement
if ($stmt = $mysqli->prepare("SELECT username FROM user WHERE state=?")) {
 
    # bind parameters
    $stmt->bind_param("s", $state);
 
    # execute
    $stmt->execute();
 
    # bind result variables
    $stmt->bind_result($username);
 
    # fetch value
    $stmt->fetch();
 
    printf("%s is from %s\n", $username, $state);
 
    $stmt->close();
}

```

### 13. 无缓冲的查询

正常的情况下，当你在当你在你的脚本中执行一个SQL语句的时候，你的程序会停在那里直到这个SQL语句返回，然后你的程序再往下继续执行。你可以使用无缓冲查询来改变这个行为。

mysql_unbuffered_query() 发送一个SQL语句到MySQL而并不像mysql_query()一样去自动fethch和缓存结果。这会相当节约很多可观的内存，尤其是那些会产生大量结果的查询语句，并且，你不需要等到所有的结果都返回，只需要第一行数据返回的时候，你就可以开始马上开始工作于查询结果了。

然而，这会有一些限制。因为你要么把所有行都读走，或是你要在进行下一次的查询前调用 mysql_free_result() 清除结果。而且， mysql_num_rows() 或 mysql_data_seek() 将无法使用。所以，是否使用无缓冲的查询你需要仔细考虑。

> This extension was deprecated in PHP 5.5.0, and it was removed in PHP 7.0.0. Instead, the MySQLi or PDO_MySQL extension should be used. 

### 14. 把IP地址存成 UNSIGNED INT
如果你用整形来存放IP地址，只需要4个字节，并且是定长的字段。而且，这会为你带来查询上的优势，尤其是当你需要使用这样的WHERE条件：IP between ip1 and ip2。

我们必需要使用UNSIGNED INT，因为 IP地址会使用整个32位的无符号整型。

你可以使用 INET_ATON() 来把一个字符串IP转成一个整型，并使用 INET_NTOA() 把一个整形转成一个字符串IP。在PHP中，也有这样的函数 ip2long() 和 long2ip()。

```php
$r = "UPDATE users SET ip = INET_ATON('{$_SERVER['REMOTE_ADDR']}') WHERE user_id = $user_id";

```
### 15. 固定长度的表会更快

如果表中的所有字段都是“固定长度”的，整个表会被认为是 “static” 或 “fixed-length”。 例如，表中没有如下类型的字段： VARCHAR，TEXT，BLOB。只要你包括了其中一个这些字段，那么这个表就不是“固定长度静态表”了，这样，MySQL 引擎会用另一种方法来处理。

固定长度的表会提高性能，因为MySQL搜寻得会更快一些，因为这些固定的长度是很容易计算下一个数据的偏移量的，所以读取的自然也会很快。而如果字段不是定长的，那么，每一次要找下一条的话，需要程序找到主键。

并且，固定长度的表也更容易被缓存和重建。不过，唯一的副作用是，固定长度的字段会浪费一些空间，因为定长的字段无论你用不用，他都是要分配那么多的空间。

使用“垂直分割”技术，你可以分割你的表成为两个一个是定长的，一个则是不定长的。

### 16. 垂直分割
“垂直分割”是一种把数据库中的表按列变成几张表的方法，这样可以降低表的复杂度和字段的数目，从而达到优化的目的。
示例一：在Users表中有一个字段是家庭地址，这个字段是可选字段，相比起，而且你在数据库操作的时候除了个人信息外，你并不需要经常读取或是改写这个字段。那么，为什么不把他放到另外一张表中呢？ 这样会让你的表有更好的性能，大量的时候，我对于用户表来说，只有用户ID，用户名，口令，用户角色等会被经常使用。小一点的表总是会有好的性能。

另外，你需要注意的是，这些被分出去的字段所形成的表，你不会经常性地去Join他们，不然的话，这样的性能会比不分割时还要差，而且，会是极数级的下降。


### 17. 拆分大的 DELETE 或 INSERT 语句
如果你需要在一个在线的网站上去执行一个大的 DELETE 或 INSERT 查询，你需要非常小心，要避免你的操作让你的整个网站停止相应。因为这两个操作是会锁表的，表一锁住了，别的操作都进不来了。

Apache 会有很多的子进程或线程。所以，其工作起来相当有效率，而我们的服务器也不希望有太多的子进程，线程和数据库链接，这是极大的占服务器资源的事情，尤其是内存。

如果你把你的表锁上一段时间，比如30秒钟，那么对于一个有很高访问量的站点来说，这30秒所积累的访问进程/线程，数据库链接，打开的文件数，可能不仅仅会让你的WEB服务Crash，还可能会让你的整台服务器马上挂掉。

所以，如果要删除一个大量数据的行记录，一定把其拆分，使用 LIMIT 条件是一个好的方法。
```php
while (1) {
    mysql_query("DELETE FROM logs WHERE log_date <= '2009-10-01' LIMIT 10000");
    if (mysql_affected_rows() == 0) {
        # done deleting
        break;
    }
    # you can even pause a bit
    usleep(50000);
}

```

### 18. 越小的列会越快

对于大多数的数据库引擎来说，硬盘操作可能是最重大的瓶颈。所以，把你的数据变得紧凑会对这种情况非常有帮助，因为这减少了对硬盘的访问。

如果一个表只会有几列罢了（比如说字典表，配置表），那么，我们就没有理由使用 INT 来做主键，使用 MEDIUMINT, SMALLINT 或是更小的 TINYINT 会更经济一些。如果你不需要记录时间，使用 DATE 要比 DATETIME 好得多。


### 19. 选择正确的存储引擎

MyISAM 适合于一些需要大量查询的应用，但其对于有大量写操作并不是很好。甚至你只是需要update一个字段，整个表都会被锁起来，而别的进程，就算是读进程都无法操作直到读操作完成。另外，MyISAM 对于 SELECT COUNT(*) 这类的计算是超快无比的。

InnoDB 的趋势会是一个非常复杂的存储引擎，对于一些小的应用，它会比 MyISAM 还慢。他是它支持“行锁” ，于是在写操作比较多的时候，会更优秀。并且，他还支持更多的高级应用，比如：事务。

### 20. 使用一个对象关系映射器（Object Relational Mapper）

使用 ORM (Object Relational Mapper)，你能够获得可靠的性能增涨。一个ORM可以做的所有事情，也能被手动的编写出来。但是，这需要一个高级专家。

ORM 的最重要的是“Lazy Loading”，也就是说，只有在需要的去取值的时候才会去真正的去做。但你也需要小心这种机制的副作用，因为这很有可能会因为要去创建很多很多小的查询反而会降低性能。

ORM 还可以把你的SQL语句打包成一个事务，这会比单独执行他们快得多得多。

### 21. 小心“永久链接”
“永久链接”的目的是用来减少重新创建MySQL链接的次数。当一个链接被创建了，它会永远处在连接的状态，就算是数据库操作已经结束了。而且，自从我们的Apache开始重用它的子进程后——也就是说，下一次的HTTP请求会重用Apache的子进程，并重用相同的 MySQL 链接。

在理论上来说，这听起来非常的不错。但是从个人经验（也是大多数人的）上来说，这个功能制造出来的麻烦事更多。因为，你只有有限的链接数，内存问题，文件句柄数，等等。

而且，Apache 运行在极端并行的环境中，会创建很多很多的了进程。这就是为什么这种“永久链接”的机制工作地不好的原因。在你决定要使用“永久链接”之前，你需要好好地考虑一下你的整个系统的架构。


> This extension was deprecated in PHP 5.5.0, and it was removed in PHP 7.0.0. Instead, the MySQLi or PDO_MySQL extension should be used.

### 友情链接

- [MYSQL性能优化的最佳20+条经验](http://coolshell.cn/articles/1846.html)([原文](https://code.tutsplus.com/tutorials/top-20-mysql-best-practices--net-7855))

---