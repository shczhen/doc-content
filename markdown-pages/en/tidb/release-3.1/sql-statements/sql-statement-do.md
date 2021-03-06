---
title: DO | TiDB SQL Statement Reference
summary: An overview of the usage of DO for the TiDB database.
aliases: ['/docs/v3.1/sql-statements/sql-statement-do/','/docs/v3.1/reference/sql/statements/do/']
---

# DO

This statement executes an expression, without returning a result. In MySQL, a common use-case is to execute stored programs without needing to handle the result. Since TiDB does not provide stored routines, this function has a more limited use.

## Synopsis

**DoStmt:**

![DoStmt](https://download.pingcap.com/images/docs/sqlgram/DoStmt.png)

**ExpressionList:**

![ExpressionList](https://download.pingcap.com/images/docs/sqlgram/ExpressionList.png)

**Expression:**

![Expression](https://download.pingcap.com/images/docs/sqlgram/Expression.png)

## Examples

```sql
mysql> SELECT SLEEP(5);
+----------+
| SLEEP(5) |
+----------+
|        0 |
+----------+
1 row in set (5.00 sec)

mysql> DO SLEEP(5);
Query OK, 0 rows affected (5.00 sec)

mysql> DO SLEEP(1), SLEEP(1.5);
Query OK, 0 rows affected (2.50 sec)
```

## MySQL compatibility

This statement is understood to be fully compatible with MySQL. Any compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [SELECT](/sql-statements/sql-statement-select.md)
