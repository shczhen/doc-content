---
title: DROP INDEX | TiDB SQL Statement Reference
summary: An overview of the usage of DROP INDEX for the TiDB database.
aliases: ['/docs/v3.0/sql-statements/sql-statement-drop-index/','/docs/v3.0/reference/sql/statements/drop-index/']
---

# DROP INDEX

This statement removes an index from a specified table, marking space as free in TiKV.

## Synopsis

**AlterTableStmt:**

![AlterTableStmt](https://download.pingcap.com/images/docs/sqlgram/AlterTableStmt.png)

**AlterTableSpec:**

![AlterTableSpec](https://download.pingcap.com/images/docs/sqlgram/AlterTableSpec.png)

**KeyOrIndex:**

![KeyOrIndex](https://download.pingcap.com/images/docs/sqlgram/KeyOrIndex.png)

**Identifier:**

![Identifier](https://download.pingcap.com/images/docs/sqlgram/Identifier.png)

## Examples

```sql
mysql> CREATE TABLE t1 (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, c1 INT NOT NULL);
Query OK, 0 rows affected (0.10 sec)

mysql> INSERT INTO t1 (c1) VALUES (1),(2),(3),(4),(5);
Query OK, 5 rows affected (0.02 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> EXPLAIN SELECT * FROM t1 WHERE c1 = 3;
+---------------------+----------+------+-------------------------------------------------------------+
| id                  | count    | task | operator info                                               |
+---------------------+----------+------+-------------------------------------------------------------+
| TableReader_7       | 10.00    | root | data:Selection_6                                            |
| └─Selection_6       | 10.00    | cop  | eq(test.t1.c1, 3)                                           |
|   └─TableScan_5     | 10000.00 | cop  | table:t1, range:[-inf,+inf], keep order:false, stats:pseudo |
+---------------------+----------+------+-------------------------------------------------------------+
3 rows in set (0.00 sec)

mysql> CREATE INDEX c1 ON t1 (c1);
Query OK, 0 rows affected (0.30 sec)

mysql> EXPLAIN SELECT * FROM t1 WHERE c1 = 3;
+-------------------+-------+------+-----------------------------------------------------------------+
| id                | count | task | operator info                                                   |
+-------------------+-------+------+-----------------------------------------------------------------+
| IndexReader_6     | 10.00 | root | index:IndexScan_5                                               |
| └─IndexScan_5     | 10.00 | cop  | table:t1, index:c1, range:[3,3], keep order:false, stats:pseudo |
+-------------------+-------+------+-----------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> ALTER TABLE t1 DROP INDEX c1;
Query OK, 0 rows affected (0.30 sec)
```

## MySQL compatibility

* Removing the primary key constraint from a column is not supported by default. You can enable the feature by setting the `alter-primary-key` configuration item to `true`. For details, see [alter-primary-key](/tidb-configuration-file.md#alter-primary-key).

## See also

* [SHOW INDEX](/sql-statements/sql-statement-show-index.md)
* [CREATE INDEX](/sql-statements/sql-statement-create-index.md)
* [ADD INDEX](/sql-statements/sql-statement-add-index.md)
* [RENAME INDEX](/sql-statements/sql-statement-rename-index.md)
