---
title: CREATE INDEX
summary: CREATE INDEX 在 TiDB 中的使用概况
aliases: ['/docs-cn/v3.0/sql-statements/sql-statement-create-index/','/docs-cn/v3.0/reference/sql/statements/create-index/']
---

# CREATE INDEX

`CREATE INDEX` 语句用于在已有表中添加新索引，功能等同于 `ALTER TABLE .. ADD INDEX`。包含该语句提供了 MySQL 兼容性。

## 语法图

**CreateIndexStmt:**

![CreateIndexStmt](https://download.pingcap.com/images/docs-cn/sqlgram/CreateIndexStmt.png)

**CreateIndexStmtUnique:**

![CreateIndexStmtUnique](https://download.pingcap.com/images/docs-cn/sqlgram/CreateIndexStmtUnique.png)

**Identifier:**

![Identifier](https://download.pingcap.com/images/docs-cn/sqlgram/Identifier.png)

**IndexTypeOpt:**

![IndexTypeOpt](https://download.pingcap.com/images/docs-cn/sqlgram/IndexTypeOpt.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs-cn/sqlgram/TableName.png)

**IndexColNameList:**

![IndexColNameList](https://download.pingcap.com/images/docs-cn/sqlgram/IndexColNameList.png)

**IndexOptionList:**

![IndexOptionList](https://download.pingcap.com/images/docs-cn/sqlgram/IndexOptionList.png)

**IndexOption:**

![IndexOption](https://download.pingcap.com/images/docs-cn/sqlgram/IndexOption.png)

## 示例


```sql
CREATE TABLE t1 (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, c1 INT NOT NULL);
```

```
Query OK, 0 rows affected (0.10 sec)
```


```sql
INSERT INTO t1 (c1) VALUES (1),(2),(3),(4),(5);
```

```
Query OK, 5 rows affected (0.02 sec)
Records: 5  Duplicates: 0  Warnings: 0
```


```sql
EXPLAIN SELECT * FROM t1 WHERE c1 = 3;
```

```
+---------------------+----------+------+-------------------------------------------------------------+
| id                  | count    | task | operator info                                               |
+---------------------+----------+------+-------------------------------------------------------------+
| TableReader_7       | 10.00    | root | data:Selection_6                                            |
| └─Selection_6       | 10.00    | cop  | eq(test.t1.c1, 3)                                           |
|   └─TableScan_5     | 10000.00 | cop  | table:t1, range:[-inf,+inf], keep order:false, stats:pseudo |
+---------------------+----------+------+-------------------------------------------------------------+
3 rows in set (0.00 sec)
```


```sql
CREATE INDEX c1 ON t1 (c1);
```

```
Query OK, 0 rows affected (0.30 sec)
```


```sql
EXPLAIN SELECT * FROM t1 WHERE c1 = 3;
```

```
+-------------------+-------+------+-----------------------------------------------------------------+
| id                | count | task | operator info                                                   |
+-------------------+-------+------+-----------------------------------------------------------------+
| IndexReader_6     | 10.00 | root | index:IndexScan_5                                               |
| └─IndexScan_5     | 10.00 | cop  | table:t1, index:c1, range:[3,3], keep order:false, stats:pseudo |
+-------------------+-------+------+-----------------------------------------------------------------+
2 rows in set (0.00 sec)
```


```sql
ALTER TABLE t1 DROP INDEX c1;
```

```
Query OK, 0 rows affected (0.30 sec)
```


```sql
CREATE UNIQUE INDEX c1 ON t1 (c1);
```

```
Query OK, 0 rows affected (0.31 sec)
```

## 相关系统变量

和 `CREATE INDEX` 语句相关的系统变量有 `tidb_ddl_reorg_worker_cnt` 、`tidb_ddl_reorg_batch_size` 和 `tidb_ddl_reorg_priority`，具体可以参考[TiDB 特定系统变量](/tidb-specific-system-variables.md#tidb_ddl_reorg_worker_cnt)。

## MySQL 兼容性

* 不支持 `FULLTEXT`，`HASH` 和 `SPATIAL` 索引。
* 不支持降序索引 （类似于 MySQL 5.7）。
* 默认无法向表中添加 `PRIMARY KEY`，在开启 `alter-primary-key` 配置项后可支持此功能，详情参考：[alter-primary-key](/tidb-configuration-file.md#alter-primary-key)。

## 另请参阅

* [ADD INDEX](/sql-statements/sql-statement-add-index.md)
* [DROP INDEX](/sql-statements/sql-statement-drop-index.md)
* [RENAME INDEX](/sql-statements/sql-statement-rename-index.md)
* [ADD COLUMN](/sql-statements/sql-statement-add-column.md)
* [CREATE TABLE](/sql-statements/sql-statement-create-table.md)
* [EXPLAIN](/sql-statements/sql-statement-explain.md)
