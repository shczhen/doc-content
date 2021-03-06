---
title: BEGIN
summary: TiDB 数据库中 BEGIN 的使用概况。
aliases: ['/docs-cn/v3.1/sql-statements/sql-statement-begin/','/docs-cn/v3.1/reference/sql/statements/begin/']
---

# BEGIN

`BEGIN` 语句用于在 TiDB 内启动一个新事务，类似于 `START TRANSACTION` 和 `SET autocommit=0` 语句。

在没有 `BEGIN` 语句的情况下，每个语句默认在各自的事务中自动提交，从而确保 MySQL 兼容性。

## 语法图

**BeginTransactionStmt:**

![BeginTransactionStmt](https://download.pingcap.com/images/docs-cn/sqlgram/BeginTransactionStmt.png)

## 示例


```sql
CREATE TABLE t1 (a int NOT NULL PRIMARY KEY);
```

```
Query OK, 0 rows affected (0.12 sec)
```


```sql
BEGIN;
```

```
Query OK, 0 rows affected (0.00 sec)
```


```sql
INSERT INTO t1 VALUES (1);
```

```
Query OK, 1 row affected (0.00 sec)
```


```sql
COMMIT;
```

```
Query OK, 0 rows affected (0.01 sec)
```

## MySQL 兼容性

TiDB 支持 `BEGIN PESSIMISTIC` 或 `BEGIN OPTIMISTIC` 的语法扩展，用户可以为某一个事务覆盖默认的事务模型。

## 另请参阅

* [COMMIT](/sql-statements/sql-statement-commit.md)
* [ROLLBACK](/sql-statements/sql-statement-rollback.md)
* [START TRANSACTION](/sql-statements/sql-statement-start-transaction.md)
