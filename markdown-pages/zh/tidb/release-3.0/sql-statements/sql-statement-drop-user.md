---
title: DROP USER
summary: TiDB 数据库中 DROP USER 的使用概况。
aliases: ['/docs-cn/v3.0/sql-statements/sql-statement-drop-user/','/docs-cn/v3.0/reference/sql/statements/drop-user/']
---

# DROP USER

`DROP USER` 语句用于从 TiDB 系统数据库中删除用户。如果用户不存在，使用关键词 `IF EXISTS` 可避免出现警告。

## 语法图

**DropUserStmt:**

![DropUserStmt](https://download.pingcap.com/images/docs-cn/sqlgram/DropUserStmt.png)

**Username:**

![Username](https://download.pingcap.com/images/docs-cn/sqlgram/Username.png)

## 示例


```sql
DROP USER idontexist;
```

```
ERROR 1396 (HY000): Operation DROP USER failed for idontexist@%
```


```sql
DROP USER IF EXISTS idontexist;
```

```
Query OK, 0 rows affected (0.01 sec)
```


```sql
CREATE USER newuser IDENTIFIED BY 'mypassword';
```

```
Query OK, 1 row affected (0.02 sec)
```


```sql
GRANT ALL ON test.* TO 'newuser';
```

```
Query OK, 0 rows affected (0.03 sec)
```


```sql
SHOW GRANTS FOR 'newuser';
```

```
+-------------------------------------------------+
| Grants for newuser@%                            |
+-------------------------------------------------+
| GRANT USAGE ON *.* TO 'newuser'@'%'             |
| GRANT ALL PRIVILEGES ON test.* TO 'newuser'@'%' |
+-------------------------------------------------+
2 rows in set (0.00 sec)
```


```sql
REVOKE ALL ON test.* FROM 'newuser';
```

```
Query OK, 0 rows affected (0.03 sec)
```


```sql
SHOW GRANTS FOR 'newuser';
```

```
+-------------------------------------+
| Grants for newuser@%                |
+-------------------------------------+
| GRANT USAGE ON *.* TO 'newuser'@'%' |
+-------------------------------------+
1 row in set (0.00 sec)
```


```sql
DROP USER newuser;
```

```
Query OK, 0 rows affected (0.14 sec)
```


```sql
SHOW GRANTS FOR newuser;
```

```
ERROR 1141 (42000): There is no such grant defined for user 'newuser' on host '%'
```

## MySQL 兼容性

* 在 TiDB 中删除不存在的用户时，使用 `IF EXISTS` 可避免出现警告。[Issue #10196](https://github.com/pingcap/tidb/issues/10196)。

## 另请参阅

* [CREATE USER](/sql-statements/sql-statement-create-user.md)
* [ALTER USER](/sql-statements/sql-statement-alter-user.md)
* [SHOW CREATE USER](/sql-statements/sql-statement-show-create-user.md)
* [Privilege Management](/privilege-management.md)
