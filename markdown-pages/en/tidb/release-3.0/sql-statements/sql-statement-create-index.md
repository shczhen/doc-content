---
title: CREATE INDEX | TiDB SQL Statement Reference
summary: An overview of the usage of CREATE INDEX for the TiDB database.
aliases: ['/docs/v3.0/sql-statements/sql-statement-create-index/','/docs/v3.0/reference/sql/statements/create-index/']
---

# CREATE INDEX

This statement adds a new index to an existing table. It is an alternative syntax to `ALTER TABLE .. ADD INDEX`, and included for MySQL compatibility.

## Synopsis

**CreateIndexStmt:**

![CreateIndexStmt](https://download.pingcap.com/images/docs/sqlgram/CreateIndexStmt.png)

**CreateIndexStmtUnique:**

![CreateIndexStmtUnique](https://download.pingcap.com/images/docs/sqlgram/CreateIndexStmtUnique.png)

**Identifier:**

![Identifier](https://download.pingcap.com/images/docs/sqlgram/Identifier.png)

**IndexTypeOpt:**

![IndexTypeOpt](https://download.pingcap.com/images/docs/sqlgram/IndexTypeOpt.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs/sqlgram/TableName.png)

**IndexColNameList:**

![IndexColNameList](https://download.pingcap.com/images/docs/sqlgram/IndexColNameList.png)

**IndexOptionList:**

![IndexOptionList](https://download.pingcap.com/images/docs/sqlgram/IndexOptionList.png)

**IndexOption:**

![IndexOption](https://download.pingcap.com/images/docs/sqlgram/IndexOption.png)

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

mysql> CREATE UNIQUE INDEX c1 ON t1 (c1);
Query OK, 0 rows affected (0.31 sec)
```

## Associated session variables

The global variables associated with the `CREATE INDEX` statement are `tidb_ddl_reorg_worker_cnt`, `tidb_ddl_reorg_batch_size` and `tidb_ddl_reorg_priority`. Refer to [TiDB-specific system variables](/tidb-specific-system-variables.md#tidb_ddl_reorg_worker_cnt) for details.

## MySQL compatibility

* `FULLTEXT`, `HASH` and `SPATIAL` indexes are not supported.
* Descending indexes are not supported (similar to MySQL 5.7).
* Adding the primary key constraint to a table is not supported by default. You can enable the feature by setting the `alter-primary-key` configuration item to `true`. For details, see [alter-primary-key](/tidb-configuration-file.md#alter-primary-key).

## See also

* [ADD INDEX](/sql-statements/sql-statement-add-index.md)
* [DROP INDEX](/sql-statements/sql-statement-drop-index.md)
* [RENAME INDEX](/sql-statements/sql-statement-rename-index.md)
* [ADD COLUMN](/sql-statements/sql-statement-add-column.md)
* [CREATE TABLE](/sql-statements/sql-statement-create-table.md)
* [EXPLAIN](/sql-statements/sql-statement-explain.md)
