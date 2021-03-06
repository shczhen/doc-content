---
title: SHOW CREATE TABLE | TiDB SQL Statement Reference
summary: An overview of the usage of SHOW CREATE TABLE for the TiDB database.
aliases: ['/docs/v3.1/sql-statements/sql-statement-show-create-table/','/docs/v3.1/reference/sql/statements/show-create-table/']
---

# SHOW CREATE TABLE

This statement shows the exact statement to recreate an existing table using SQL.

## Synopsis

**ShowStmt:**

![ShowStmt](https://download.pingcap.com/images/docs/sqlgram/ShowStmt.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs/sqlgram/TableName.png)

## Examples

```sql
mysql> CREATE TABLE t1 (a INT);
Query OK, 0 rows affected (0.12 sec)

mysql> SHOW CREATE TABLE t1;
+-------+------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                               |
+-------+------------------------------------------------------------------------------------------------------------+
| t1    | CREATE TABLE `t1` (
  `a` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin |
+-------+------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

## MySQL compatibility

This statement is understood to be fully compatible with MySQL. Any compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [CREATE TABLE](/sql-statements/sql-statement-create-table.md)
* [DROP TABLE](/sql-statements/sql-statement-drop-table.md)
* [SHOW TABLES](/sql-statements/sql-statement-show-tables.md)
* [SHOW COLUMNS FROM](/sql-statements/sql-statement-show-columns-from.md)
