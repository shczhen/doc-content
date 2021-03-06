---
title: FLUSH TABLES
summary: TiDB 数据库中 FLUSH TABLES 的使用概况。
aliases: ['/docs-cn/v2.1/sql-statements/sql-statement-flush-tables/','/docs-cn/v2.1/reference/sql/statements/flush-tables/']
---

# FLUSH TABLES

`FLUSH TABLES` 语句用于提供 MySQL 兼容性，但在 TiDB 中并无有效用途。

## 语法图

**FlushStmt:**

![FlushStmt](https://download.pingcap.com/images/docs-cn/sqlgram/FlushStmt.png)

**NoWriteToBinLogAliasOpt:**

![NoWriteToBinLogAliasOpt](https://download.pingcap.com/images/docs-cn/sqlgram/NoWriteToBinLogAliasOpt.png)

**FlushOption:**

![FlushOption](https://download.pingcap.com/images/docs-cn/sqlgram/FlushOption.png)

**TableOrTables:**

![TableOrTables](https://download.pingcap.com/images/docs-cn/sqlgram/TableOrTables.png)

**TableNameListOpt:**

![TableNameListOpt](https://download.pingcap.com/images/docs-cn/sqlgram/TableNameListOpt.png)

**WithReadLockOpt:**

![WithReadLockOpt](https://download.pingcap.com/images/docs-cn/sqlgram/WithReadLockOpt.png)

## 示例

```sql
mysql> FLUSH TABLES;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH TABLES WITH READ LOCK;
ERROR 1105 (HY000): FLUSH TABLES WITH READ LOCK is not supported.  Please use @@tidb_snapshot
```

## MySQL 兼容性

* TiDB 没有 MySQL 中的表缓存这一概念。所以，`FLUSH TABLES` 因 MySQL 兼容性会在 TiDB 中解析出但会被忽略掉。
* 因为 TiDB 目前不支持锁表，所以`FLUSH TABLES WITH READ LOCK` 语句会产生错误。建议使用 [Historical reads](/read-historical-data.md) 来实现锁表。

## 另请参阅

* [Read historical data](/read-historical-data.md)
