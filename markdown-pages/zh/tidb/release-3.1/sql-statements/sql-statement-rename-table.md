---
title: RENAME TABLE
summary: TiDB 数据库中 RENAME TABLE 的使用概况。
aliases: ['/docs-cn/v3.1/sql-statements/sql-statement-rename-table/','/docs-cn/v3.1/reference/sql/statements/rename-table/']
---

# RENAME TABLE

`RENAME TABLE` 语句用于对已有表进行重命名。

## 语法图

**RenameTableStmt:**

![RenameTableStmt](https://download.pingcap.com/images/docs-cn/sqlgram/RenameTableStmt.png)

**TableToTable:**

![TableToTable](https://download.pingcap.com/images/docs-cn/sqlgram/TableToTable.png)

## 示例


```sql
CREATE TABLE t1 (a int);
```

```
Query OK, 0 rows affected (0.12 sec)
```


```sql
SHOW TABLES;
```

```
+----------------+
| Tables_in_test |
+----------------+
| t1             |
+----------------+
1 row in set (0.00 sec)
```


```sql
RENAME TABLE t1 TO t2;
```

```
Query OK, 0 rows affected (0.08 sec)
```


```sql
SHOW TABLES;
```

```
+----------------+
| Tables_in_test |
+----------------+
| t2             |
+----------------+
1 row in set (0.00 sec)
```

## MySQL 兼容性

`RENAME TABLE` 语句与 MySQL 完全兼容。如发现任何兼容性差异，请在 GitHub 上提交 [issue](https://github.com/pingcap/tidb/issues/new/choose)。

## 另请参阅

* [CREATE TABLE](/sql-statements/sql-statement-create-table.md)
* [SHOW TABLES](/sql-statements/sql-statement-show-tables.md)
* [ALTER TABLE](/sql-statements/sql-statement-alter-table.md)
