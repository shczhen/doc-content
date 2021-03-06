---
title: EXECUTE
summary: TiDB 数据库中 EXECUTE 的使用概况。
aliases: ['/docs-cn/v3.0/sql-statements/sql-statement-execute/','/docs-cn/v3.0/reference/sql/statements/execute/']
---

# EXECUTE

`EXECUTE` 语句为服务器端预处理语句提供 SQL 接口。

## 语法图

**ExecuteStmt:**

![ExecuteStmt](https://download.pingcap.com/images/docs-cn/sqlgram/ExecuteStmt.png)

**Identifier:**

![Identifier](https://download.pingcap.com/images/docs-cn/sqlgram/Identifier.png)

## 示例


```sql
PREPARE mystmt FROM 'SELECT ? as num FROM DUAL';
```

```
Query OK, 0 rows affected (0.00 sec)
```


```sql
SET @number = 5;
```

```
Query OK, 0 rows affected (0.00 sec)
```


```sql
EXECUTE mystmt USING @number;
```

```
+------+
| num  |
+------+
| 5    |
+------+
1 row in set (0.00 sec)
```


```sql
DEALLOCATE PREPARE mystmt;
```

```
Query OK, 0 rows affected (0.00 sec)
```

## MySQL 兼容性

`EXECUTE` 语句与 MySQL 完全兼容。如发现任何兼容性差异，请在 GitHub 上提交 [issue](https://github.com/pingcap/tidb/issues/new/choose)。

## 另请参阅

* [PREPARE](/sql-statements/sql-statement-prepare.md)
* [DEALLOCATE](/sql-statements/sql-statement-deallocate.md)
