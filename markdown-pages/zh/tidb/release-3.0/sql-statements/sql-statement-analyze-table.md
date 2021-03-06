---
title: ANALYZE TABLE
summary: TiDB 数据库中 ANALYZE TABLE 的使用概况。
aliases: ['/docs-cn/v3.0/sql-statements/sql-statement-analyze-table/','/docs-cn/v3.0/reference/sql/statements/analyze-table/']
---

# ANALYZE TABLE

`ANALYZE TABLE` 语句用于更新 TiDB 在表和索引上留下的统计信息。执行大批量更新或导入记录后，或查询执行计划不是最佳时，建议运行 `ANALYZE TABLE`。

当 TiDB 逐渐发现这些统计数据与预估不一致时，也会自动更新其统计数据。

## 语法图

```ebnf+diagram
AnalyzeTableStmt ::=
    'ANALYZE' ( 'TABLE' ( TableNameList | TableName ( 'INDEX' IndexNameList | 'PARTITION' PartitionNameList ( 'INDEX' IndexNameList )? ) ) | 'INCREMENTAL' 'TABLE' TableName ( 'PARTITION' PartitionNameList )? 'INDEX' IndexNameList ) AnalyzeOptionListOpt

TableNameList ::=
    TableName (',' TableName)*

TableName ::=
    Identifier ( '.' Identifier )?
```

## 示例


```sql
CREATE TABLE t1 (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, c1 INT NOT NULL);
```

```
Query OK, 0 rows affected (0.11 sec)
```


```sql
INSERT INTO t1 (c1) VALUES (1),(2),(3),(4),(5);
```

```
Query OK, 5 rows affected (0.03 sec)
Records: 5  Duplicates: 0  Warnings: 0
```


```sql
ALTER TABLE t1 ADD INDEX (c1);
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
analyze table t1;
```

```
Query OK, 0 rows affected (0.13 sec)
```


```sql
EXPLAIN SELECT * FROM t1 WHERE c1 = 3;
```

```
+-------------------+-------+------+---------------------------------------------------+
| id                | count | task | operator info                                     |
+-------------------+-------+------+---------------------------------------------------+
| IndexReader_6     | 1.00  | root | index:IndexScan_5                                 |
| └─IndexScan_5     | 1.00  | cop  | table:t1, index:c1, range:[3,3], keep order:false |
+-------------------+-------+------+---------------------------------------------------+
2 rows in set (0.00 sec)
```

## MySQL 兼容性

`ANALYZE TABLE` 在语法上与 MySQL 类似。但 `ANALYZE TABLE` 在 TiDB 上执行所需时间可能长得多，因为它的内部运行方式不同。

## 另请参阅

* [EXPLAIN](/sql-statements/sql-statement-explain.md)
* [EXPLAIN ANALYZE](/sql-statements/sql-statement-explain-analyze.md)
