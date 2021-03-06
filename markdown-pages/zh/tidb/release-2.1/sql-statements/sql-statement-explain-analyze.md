---
title: EXPLAIN ANALYZE
summary: TiDB 数据库中 EXPLAIN ANALYZE 的使用概况。
aliases: ['/docs-cn/v2.1/sql-statements/sql-statement-explain-analyze/','/docs-cn/v2.1/reference/sql/statements/explain-analyze/']
---

# EXPLAIN ANALYZE

`EXPLAIN ANALYZE` 语句的工作方式类似于 `EXPLAIN`，主要区别在于前者实际上会执行语句。这样可以将查询计划中的估计值与执行时所遇到的实际值进行比较。如果估计值与实际值显著不同，那么应考虑在受影响的表上运行 `ANALYZE TABLE`。

## 语法图

**ExplainSym:**

![ExplainSym](https://download.pingcap.com/images/docs-cn/sqlgram/ExplainSym.png)

**ExplainStmt:**

![ExplainStmt](https://download.pingcap.com/images/docs-cn/sqlgram/ExplainStmt.png)

**ExplainableStmt:**

![ExplainableStmt](https://download.pingcap.com/images/docs-cn/sqlgram/ExplainableStmt.png)

## 示例

```sql
mysql> CREATE TABLE t1 (id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, c1 INT NOT NULL);
Query OK, 0 rows affected (0.12 sec)

mysql> INSERT INTO t1 (c1) VALUES (1), (2), (3);
Query OK, 3 rows affected (0.02 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> EXPLAIN ANALYZE SELECT * FROM t1 WHERE id = 1;
+-------------+-------+------+--------------------+---------------------------+
| id          | count | task | operator info      | execution info            |
+-------------+-------+------+--------------------+---------------------------+
| Point_Get_1 | 1.00  | root | table:t1, handle:1 | time:0ns, loops:0, rows:0 |
+-------------+-------+------+--------------------+---------------------------+
1 row in set (0.01 sec)

mysql> EXPLAIN ANALYZE SELECT * FROM t1;
+-------------------+----------+------+-------------------------------------------------------------+----------------------------------+
| id                | count    | task | operator info                                               | execution info                   |
+-------------------+----------+------+-------------------------------------------------------------+----------------------------------+
| TableReader_5     | 10000.00 | root | data:TableScan_4                                            | time:931.759µs, loops:2, rows:3  |
| └─TableScan_4     | 10000.00 | cop  | table:t1, range:[-inf,+inf], keep order:false, stats:pseudo | time:0s, loops:0, rows:3         |
+-------------------+----------+------+-------------------------------------------------------------+----------------------------------+
2 rows in set (0.00 sec)
```

## MySQL 兼容性

`EXPLAIN ANALYZE` 是 MySQL 8.0 的功能，但该语句在 MySQL 中的输出格式和可能的执行计划都与 TiDB 有较大差异。

## 另请参阅

* [Understanding the Query Execution Plan](/query-execution-plan.md)
* [EXPLAIN](/sql-statements/sql-statement-explain.md)
* [ANALYZE TABLE](/sql-statements/sql-statement-analyze-table.md)
* [TRACE](/sql-statements/sql-statement-trace.md)