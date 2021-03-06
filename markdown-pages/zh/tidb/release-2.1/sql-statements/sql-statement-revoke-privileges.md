---
title: REVOKE <privileges>
summary: TiDB 数据库中 REVOKE <privileges> 的使用概况。
aliases: ['/docs-cn/v2.1/sql-statements/sql-statement-revoke-privileges/','/docs-cn/v2.1/reference/sql/statements/revoke-privileges/']
---

# `REVOKE <privileges>`

`REVOKE <privileges>` 语句用于删除已有用户的权限。

## 语法图

**GrantStmt:**

![GrantStmt](https://download.pingcap.com/images/docs-cn/sqlgram/GrantStmt.png)

**PrivElemList:**

![PrivElemList](https://download.pingcap.com/images/docs-cn/sqlgram/PrivElemList.png)

**PrivElem:**

![PrivElem](https://download.pingcap.com/images/docs-cn/sqlgram/PrivElem.png)

**PrivType:**

![PrivType](https://download.pingcap.com/images/docs-cn/sqlgram/PrivType.png)

**ObjectType:**

![ObjectType](https://download.pingcap.com/images/docs-cn/sqlgram/ObjectType.png)

**PrivLevel:**

![PrivLevel](https://download.pingcap.com/images/docs-cn/sqlgram/PrivLevel.png)

**UserSpecList:**

![UserSpecList](https://download.pingcap.com/images/docs-cn/sqlgram/UserSpecList.png)

## 示例

```sql
mysql> CREATE USER newuser IDENTIFIED BY 'mypassword';
Query OK, 1 row affected (0.02 sec)

mysql> GRANT ALL ON test.* TO 'newuser';
Query OK, 0 rows affected (0.03 sec)

mysql> SHOW GRANTS FOR 'newuser';
+-------------------------------------------------+
| Grants for newuser@%                            |
+-------------------------------------------------+
| GRANT USAGE ON *.* TO 'newuser'@'%'             |
| GRANT ALL PRIVILEGES ON test.* TO 'newuser'@'%' |
+-------------------------------------------------+
2 rows in set (0.00 sec)

mysql> REVOKE ALL ON test.* FROM 'newuser';
Query OK, 0 rows affected (0.03 sec)

mysql> SHOW GRANTS FOR 'newuser';
+-------------------------------------+
| Grants for newuser@%                |
+-------------------------------------+
| GRANT USAGE ON *.* TO 'newuser'@'%' |
+-------------------------------------+
1 row in set (0.00 sec)

mysql> DROP USER newuser;
Query OK, 0 rows affected (0.14 sec)

mysql> SHOW GRANTS FOR newuser;
ERROR 1141 (42000): There is no such grant defined for user 'newuser' on host '%'
```

## MySQL 兼容性

`REVOKE <privileges>` 语句与 MySQL 完全兼容。如发现任何兼容性差异，请在 GitHub 上提交 [issue](https://github.com/pingcap/tidb/issues/new/choose)。

## 另请参阅

* [`GRANT <privileges>`](/sql-statements/sql-statement-grant-privileges.md)
* [SHOW GRANTS](/sql-statements/sql-statement-show-grants.md)
* [Privilege Management](/privilege-management.md)
