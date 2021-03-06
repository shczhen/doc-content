---
title: SHOW INDEXES [FROM|IN] | TiDB SQL Statement Reference
summary: An overview of the usage of SHOW INDEXES [FROM|IN] for the TiDB database.
aliases: ['/docs/stable/sql-statements/sql-statement-show-indexes/','/docs/v4.0/sql-statements/sql-statement-show-indexes/','/docs/stable/reference/sql/statements/show-indexes/']
---

# SHOW INDEXES [FROM|IN]

The statement `SHOW INDEXES [FROM|IN]` lists the indexes on a specified table.  The statements `SHOW INDEX [FROM|IN]`, `SHOW KEYS [FROM|IN]` are aliases of this statement, and included for compatibility with MySQL.

## Synopsis

**ShowIndexStmt:**

![ShowIndexStmt](https://download.pingcap.com/images/docs/sqlgram/ShowIndexStmt.png)

**ShowIndexKwd:**

![ShowIndexKwd](https://download.pingcap.com/images/docs/sqlgram/ShowIndexKwd.png)

**FromOrIn:**

![FromOrIn](https://download.pingcap.com/images/docs/sqlgram/FromOrIn.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs/sqlgram/TableName.png)

**ShowLikeOrWhereOpt:**

![ShowLikeOrWhereOpt](https://download.pingcap.com/images/docs/sqlgram/ShowLikeOrWhereOpt.png)

## Examples

```sql
mysql> CREATE TABLE t1 (id int not null primary key AUTO_INCREMENT, col1 INT, INDEX(col1));
Query OK, 0 rows affected (0.12 sec)

mysql> SHOW INDEXES FROM t1;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| t1    |          0 | PRIMARY  |            1 | id          | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
| t1    |          1 | col1     |            1 | col1        | A         |           0 |     NULL | NULL   | YES  | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)

mysql> SHOW INDEX FROM t1;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| t1    |          0 | PRIMARY  |            1 | id          | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
| t1    |          1 | col1     |            1 | col1        | A         |           0 |     NULL | NULL   | YES  | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)

mysql> SHOW KEYS FROM t1;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| t1    |          0 | PRIMARY  |            1 | id          | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
| t1    |          1 | col1     |            1 | col1        | A         |           0 |     NULL | NULL   | YES  | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)
```

## MySQL compatibility

This statement is understood to be fully compatible with MySQL. Any compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [SHOW CREATE TABLE](/sql-statements/sql-statement-show-create-table.md)
* [DROP INDEX](/sql-statements/sql-statement-drop-index.md)
* [CREATE INDEX](/sql-statements/sql-statement-create-index.md)
