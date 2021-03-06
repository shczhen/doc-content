---
title: INSERT
summary: TiDB 数据库中 INSERT 的使用概况。
aliases: ['/docs-cn/v3.1/sql-statements/sql-statement-insert/','/docs-cn/v3.1/reference/sql/statements/insert/']
---

# INSERT

使用 `INSERT` 语句在表中插入新行。

## 语法图

**InsertIntoStmt:**

![InsertIntoStmt](https://download.pingcap.com/images/docs-cn/sqlgram/InsertIntoStmt.png)

**PriorityOpt:**

![PriorityOpt](https://download.pingcap.com/images/docs-cn/sqlgram/PriorityOpt.png)

**IgnoreOptional:**

![IgnoreOptional](https://download.pingcap.com/images/docs-cn/sqlgram/IgnoreOptional.png)

**IntoOpt:**

![IntoOpt](https://download.pingcap.com/images/docs-cn/sqlgram/IntoOpt.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs-cn/sqlgram/TableName.png)

**InsertValues:**

![InsertValues](https://download.pingcap.com/images/docs-cn/sqlgram/InsertValues.png)

## 示例


```sql
CREATE TABLE t1 (a int);
```

```
Query OK, 0 rows affected (0.11 sec)
```


```sql
CREATE TABLE t2 LIKE t1;
```

```
Query OK, 0 rows affected (0.11 sec)
```


```sql
INSERT INTO t1 VALUES (1);
```

```
Query OK, 1 row affected (0.02 sec)
```


```sql
INSERT INTO t1 (a) VALUES (1);
```

```
Query OK, 1 row affected (0.01 sec)
```


```sql
INSERT INTO t2 SELECT * FROM t1;
```

```
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0
```


```sql
SELECT * FROM t1;
```

```
+------+
| a    |
+------+
|    1 |
|    1 |
+------+
2 rows in set (0.00 sec)
```


```sql
SELECT * FROM t2;
```

```
+------+
| a    |
+------+
|    1 |
|    1 |
+------+
2 rows in set (0.00 sec)
```


```sql
INSERT INTO t2 VALUES (2),(3),(4);
```

```
Query OK, 3 rows affected (0.02 sec)
Records: 3  Duplicates: 0  Warnings: 0
```


```sql
SELECT * FROM t2;
```

```
+------+
| a    |
+------+
|    1 |
|    1 |
|    2 |
|    3 |
|    4 |
+------+
5 rows in set (0.00 sec)
```

## MySQL 兼容性

`INSERT` 语句与 MySQL 完全兼容。如发现任何兼容性差异，请在 GitHub 上提交 [issue](https://github.com/pingcap/tidb/issues/new/choose)。

## 另请参阅

* [DELETE](/sql-statements/sql-statement-delete.md)
* [SELECT](/sql-statements/sql-statement-select.md)
* [UPDATE](/sql-statements/sql-statement-update.md)
* [REPLACE](/sql-statements/sql-statement-replace.md)
