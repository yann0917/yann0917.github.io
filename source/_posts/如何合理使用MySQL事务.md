---
title: 如何合理使用MySQL事务
date: 2020-01-12 10:22:36
tags: MySQL
categories: MySQL
---

## MySQL事务隔离级别

通过 `select @@tx_isolation;` 可查看事务的隔离级别，由低到高依次为：

1. 读未提交 `READ-UNCOMMITTED`
2. 读已提交 `READ-COMMITTED`
3. 可重复读 `REPEATABLE-READ`（默认）
4. 可串行化 `SERIALIZABLE`

低级别的隔离级一般支持更高的并发处理，并拥有更低的系统开销。

### 隔离级别产生的问题

> 可解决多个事务并发存在的脏读(Dirty Read)，不可重复读(Nonrepeatable Read)，幻读(Phantom Read)等问题。

* **脏读(Dirty Read)**: A事务已更新一份数据，B事务在此时读取了同一份数据，由于某些原因，A 事务RollBack操作，则B事务所读取的数据就会是不正确的;
* **不可重复读(Nonrepeatable Read)**: A事务先读取一份数据，B 事务更新数据，A 事务再次读同一份数据。A事务的两次查询之中数据不一致，这可能是两次查询过程中间插入了一个事务更新的原有的数据;
* **幻读(Phantom Read)**: 在一个事务的两次查询中数据笔数不一致，例如有一个事务查询了几列(Row)数据，而另一个事务却在此时插入了新的几列数据，先前的事务在接下来的查询中，就会发现有几列数据是它先前所没有的;


| 隔离级别         | 脏读 | 不可重复读 | 幻读 |
|------------------|------|------------|------|
| READ-UNCOMMITTED | yes  | yes        | yes  |
| READ-COMMITTED   | no   | yes        | yes  |
| REPEATABLE-READ  | no   | no         | yes* |
| SERIALIZABLE     | no   | no         | no   |

注：`REPEATABLE-READ` 下，innodb 引擎下的幻读是由 `MVCC` 或者 `GAP 锁` 或者是 `next-key lock` 解决的

## 参考

1. [Innodb 事务隔离等级](https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html)
2. [Innodb 中 RR 隔离级别能否防止幻读](https://github.com/Yhzhtk/note/issues/42)