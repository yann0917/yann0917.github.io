---
title: 姜承尧的MySQL实战宝典笔记
tags:
  - MySQL
categories: MySQL
abbrlink: 53009
date: 2021-05-29 14:02:43
---

> 01~07 表设计篇
>
> 08~14 索引调优篇
>
> 15~21 高可用架构篇
>
> 22~28 分布式架构篇
>
> 29~36 终极实战篇

## 01数字类型：避免自增踩坑

* 不推荐使用整型类型的属性 Unsigned，若非要使用，参数 sql_mode 务必额外添加上选项 `NO_UNSIGNED_SUBTRACTION`；

* 自增整型类型做主键，**务必使用类型 BIGINT**，而非 INT，后期表结构调整代价巨大；

* MySQL 8.0 版本前，**自增整型会有回溯问题，做业务开发的你一定要了解这个问题**；

* 当达到自增整型类型的上限值时，再次自增插入，MySQL 数据库会报重复错误；

* 不要再使用浮点类型 Float、Double，MySQL 后续版本将不再支持上述两种类型；

* 账户余额字段，设计是用`整型类型`，而不是 `DECIMAL` 类型，这样性能更好，存储更紧凑

## 02字符串类型：不能忽略的 COLLATION

* CHAR 和 VARCHAR 虽然分别用于存储定长和变长字符，但对于变长字符集（如 GBK、UTF8MB4），**其本质是一样的，都是变长，设计时完全可以用 VARCHAR 替代 CHAR**；

* 推荐 MySQL 字符集默认设置为 `UTF8MB4`，可以用于存储 emoji 等扩展字符；

* 排序规则很重要，用于字符的比较和排序，但大部分场景不需要用区分大小写的排序规则；

* 修改表中已有列的字符集，使用命令 ALTER TABLE ... CONVERT TO .... eg: `ALTER TABLE emoji_test CONVERT TO CHARSET utf8mb4;`；

* 用户性别，运行状态等有限值的列，MySQL 8.0.16 版本直接使用 CHECK 约束机制，之前的版本可使用 ENUM 枚举字符串类型，外加 SQL_MODE 的严格模式；

* 业务隐私信息，如密码、手机、信用卡等信息，需要加密。切记简单的MD5算法是可以进行暴力破解，并不安全，推荐使用**动态盐+动态加密算法**进行隐私数据的存储

## 03日期类型：TIMESTAMP 可能是巨坑

* MySQL 5.6 版本开始 DATETIME 和 TIMESTAMP 精度支持到毫秒；

* DATETIME 占用 8 个字节，TIMESTAMP 占用 4 个字节，DATETIME(6) 依然占用 8 个字节，TIMESTAMP(6) 占用 7 个字节；

* TIMESTAMP 日期存储的上限为 `2038-01-19 03:14:07`，业务用 TIMESTAMP 存在风险；

* 使用 TIMESTAMP 必须显式地设置时区，不要使用默认系统时区，否则存在性能问题，推荐在配置文件中设置参数 `time_zone = '+08:00'`；

* 推荐日期类型使用 DATETIME，而不是 TIMESTAMP 和 INT 类型；

* 表结构设计时，每个核心业务表，推荐设计一个 last_modify_date 的字段，用以记录每条记录的最后修改时间

