---
title: DROP INDEX
summary: TiDB 数据库中 DROP INDEX 的使用概况。
aliases: ['/docs-cn/v3.1/sql-statements/sql-statement-drop-index/','/docs-cn/v3.1/reference/sql/statements/drop-index/']
---

# DROP INDEX

`DROP INDEX` 语句用于从指定的表中删除索引，并在 TiKV 中将空间标记为释放。

## 语法图

**AlterTableStmt:**

![AlterTableStmt](https://download.pingcap.com/images/docs-cn/sqlgram/AlterTableStmt.png)

**AlterTableSpec:**

![AlterTableSpec](https://download.pingcap.com/images/docs-cn/sqlgram/AlterTableSpec.png)

**KeyOrIndex:**

![KeyOrIndex](https://download.pingcap.com/images/docs-cn/sqlgram/KeyOrIndex.png)

**Identifier:**

![Identifier](https://download.pingcap.com/images/docs-cn/sqlgram/Identifier.png)

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

## MySQL 兼容性

* 默认不支持删除 `PRIMARY KEY`，在开启 `alter-primary-key` 配置项后可支持此功能，详情参考：[alter-primary-key](/tidb-configuration-file.md#alter-primary-key)。

## 另请参阅

* [SHOW INDEX](/sql-statements/sql-statement-show-index.md)
* [CREATE INDEX](/sql-statements/sql-statement-create-index.md)
* [ADD INDEX](/sql-statements/sql-statement-add-index.md)
* [RENAME INDEX](/sql-statements/sql-statement-rename-index.md)
