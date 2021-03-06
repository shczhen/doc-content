---
title: MODIFY COLUMN
summary: TiDB 数据库中 MODIFY COLUMN 的使用概况。
aliases: ['/docs-cn/v3.0/sql-statements/sql-statement-modify-column/','/docs-cn/v3.0/reference/sql/statements/modify-column/']
---

# MODIFY COLUMN

`ALTER TABLE .. MODIFY COLUMN` 语句用于修改已有表上的列，包括列的数据类型和属性。若要同时重命名，可改用 [`CHANGE COLUMN`](/sql-statements/sql-statement-change-column.md) 语句。

## 语法图

**AlterTableStmt:**

![AlterTableStmt](https://download.pingcap.com/images/docs-cn/sqlgram/AlterTableStmt.png)

**AlterTableSpec:**

![AlterTableSpec](https://download.pingcap.com/images/docs-cn/sqlgram/AlterTableSpec.png)

**ColumnKeywordOpt:**

![ColumnKeywordOpt](https://download.pingcap.com/images/docs-cn/sqlgram/ColumnKeywordOpt.png)

**ColumnDef:**

![ColumnDef](https://download.pingcap.com/images/docs-cn/sqlgram/ColumnDef.png)

**ColumnPosition:**

![ColumnPosition](https://download.pingcap.com/images/docs-cn/sqlgram/ColumnPosition.png)

## 示例


```sql
CREATE TABLE t1 (id int not null primary key AUTO_INCREMENT, col1 INT);
```

```
Query OK, 0 rows affected (0.11 sec)
```


```sql
INSERT INTO t1 (col1) VALUES (1),(2),(3),(4),(5);
```

```
Query OK, 5 rows affected (0.02 sec)
Records: 5  Duplicates: 0  Warnings: 0
```


```sql
ALTER TABLE t1 MODIFY col1 BIGINT;
```

```
Query OK, 0 rows affected (0.09 sec)
```


```sql
SHOW CREATE TABLE t1;
```

```
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `col1` bigint(20) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin AUTO_INCREMENT=30001
1 row in set (0.00 sec)
```


```sql
ALTER TABLE t1 MODIFY col1 INT;
```

```
ERROR 1105 (HY000): unsupported modify column length 11 is less than origin 20
```


```sql
ALTER TABLE t1 MODIFY col1 BLOB;
```

```
ERROR 1105 (HY000): unsupported modify column type 252 not match origin 8
```


```sql
ALTER TABLE t1 MODIFY col1 BIGINT, MODIFY id BIGINT NOT NULL;
```

```
ERROR 1105 (HY000): can't run multi schema change
```

## MySQL 兼容性

* 目前不支持使用单个 `ALTER TABLE` 语句进行多个修改。
* 仅支持特定类型的数据类型更改。例如，支持将 `INTEGER` 改为 `BIGINT`，但不支持将 `BIGINT` 改为 `INTEGER`。不支持将整数改为字符串格式或 BLOB 格式。
* 不支持修改 decimal 类型的精度。

## 另请参阅

* [CREATE TABLE](/sql-statements/sql-statement-create-table.md)
* [SHOW CREATE TABLE](/sql-statements/sql-statement-show-create-table.md)
* [ADD COLUMN](/sql-statements/sql-statement-add-column.md)
* [DROP COLUMN](/sql-statements/sql-statement-drop-column.md)
* [CHANGE COLUMN](/sql-statements/sql-statement-change-column.md)
