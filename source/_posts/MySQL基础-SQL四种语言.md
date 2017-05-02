---
title: MySQL基础-SQL四种语言
tags: MySQL
categories: MySQL
abbrlink: 58504
date: 2017-05-02 11:43:58
---

### SQL 的四种语言
> - DDL (Data Definition Language) 数据库定义语言
> - DML (Data Manipulation Language) 数据操作语言
> - DCL (Data Control Language) 数据库控制语言
> - TCL (Transaction Control Language) 事务控制语言

### DDL
用于定义SQL模式、基本表、视图和索引的创建和撤消操作，在建表的时候使用。
- CREATE
- ALTER
- DROP
- TRUNCATE
- COMMIT
- RENAME


### DML
数据操纵分成数据查询和数据更新两类。数据更新又分成插入、删除、和修改三种操作。
- SELECT
- INSERT
- UPDATE
- DELETE
- MERGE
- CALL
- EXPLAIN PLAN
- LOCK TABLE
SELECT 也可以称为DQL语言

### DCL
包括对基本表和视图的授权，完整性规则的描述，事务控制等内容。
- GRANT
- REVOKE


### TCL
事务控制，设置保存点，回滚，改变事务选项，保存以完成的工作等
- COMMIT
- SAVEPOINT
- ROLLBACK
- SET TRANSACTION

---