---
title: COMMIT | TiDB SQL Statement Reference
summary: An overview of the usage of COMMIT for the TiDB database.
aliases: ['/docs/v3.1/sql-statements/sql-statement-commit/','/docs/v3.1/reference/sql/statements/commit/']
---

# COMMIT

This statement commits a transaction inside of the TIDB server.

In the absence of a `BEGIN` or `START TRANSACTION` statement, the default behavior of TiDB is that every statement will be its own transaction and autocommit. This behavior ensures MySQL compatibility.

## Synopsis

**CommitStmt:**

![CommitStmt](https://download.pingcap.com/images/docs/sqlgram/CommitStmt.png)

## Examples

```sql
mysql> CREATE TABLE t1 (a int NOT NULL PRIMARY KEY);
Query OK, 0 rows affected (0.12 sec)

mysql> START TRANSACTION;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t1 VALUES (1);
Query OK, 1 row affected (0.00 sec)

mysql> COMMIT;
Query OK, 0 rows affected (0.01 sec)
```

## MySQL compatibility

* By default, TiDB 3.0.8 and later versions use [Pessimistic Locking](/pessimistic-transaction.md). When using [Optimistic Locking](/optimistic-transaction.md), it is important to consider that a `COMMIT` statement might fail because rows have been modified by another transaction.
* When Optimistic Locking is enabled, `UNIQUE` and `PRIMARY KEY` constraint checks are deferred until statement commit. This results in additional situations where a `COMMIT` statement might fail. This behavior can be changed by setting `tidb_constraint_check_in_place=TRUE`.

## See also

* [START TRANSACTION](/sql-statements/sql-statement-start-transaction.md)
* [ROLLBACK](/sql-statements/sql-statement-rollback.md)
* [BEGIN](/sql-statements/sql-statement-begin.md)
* [Lazy checking of constraints](/transaction-overview.md#lazy-check-of-constraints)
