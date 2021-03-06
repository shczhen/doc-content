---
title: REPLACE | TiDB SQL Statement Reference
summary: An overview of the usage of REPLACE for the TiDB database.
aliases: ['/docs/v3.0/sql-statements/sql-statement-replace/','/docs/v3.0/reference/sql/statements/replace/']
---

# REPLACE

The `REPLACE` statement is semantically a combined `DELETE`+`INSERT` statement. It can be used to simplify application code.

## Synopsis

**ReplaceIntoStmt:**

![ReplaceIntoStmt](https://download.pingcap.com/images/docs/sqlgram/ReplaceIntoStmt.png)

**PriorityOpt:**

![PriorityOpt](https://download.pingcap.com/images/docs/sqlgram/PriorityOpt.png)

**IntoOpt:**

![IntoOpt](https://download.pingcap.com/images/docs/sqlgram/IntoOpt.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs/sqlgram/TableName.png)

**InsertValues:**

![InsertValues](https://download.pingcap.com/images/docs/sqlgram/InsertValues.png)

## Examples

```sql
mysql> CREATE TABLE t1 (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, c1 INT NOT NULL);
Query OK, 0 rows affected (0.12 sec)

mysql> INSERT INTO t1 (c1) VALUES (1), (2), (3);
Query OK, 3 rows affected (0.02 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM t1;
+----+----+
| id | c1 |
+----+----+
|  1 |  1 |
|  2 |  2 |
|  3 |  3 |
+----+----+
3 rows in set (0.00 sec)

mysql> REPLACE INTO t1 (id, c1) VALUES(3, 99);
Query OK, 2 rows affected (0.01 sec)

mysql> SELECT * FROM t1;
+----+----+
| id | c1 |
+----+----+
|  1 |  1 |
|  2 |  2 |
|  3 | 99 |
+----+----+
3 rows in set (0.00 sec)
```

## MySQL compatibility

This statement is understood to be fully compatible with MySQL. Any compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [DELETE](/sql-statements/sql-statement-delete.md)
* [INSERT](/sql-statements/sql-statement-insert.md)
* [SELECT](/sql-statements/sql-statement-select.md)
* [UPDATE](/sql-statements/sql-statement-update.md)
