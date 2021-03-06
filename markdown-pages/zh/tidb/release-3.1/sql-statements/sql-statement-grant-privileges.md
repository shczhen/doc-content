---
title: GRANT <privileges>
summary: TiDB 数据库中 GRANT <privileges> 的使用概况。
aliases: ['/docs-cn/v3.1/sql-statements/sql-statement-grant-privileges/','/docs-cn/v3.1/reference/sql/statements/grant-privileges/']
---

# `GRANT <privileges>`

`GRANT <privileges>` 语句用于为 TiDB 中已存在的用户分配权限。TiDB 中的权限系统同 MySQL 一样，都基于数据库/表模式来分配凭据。

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

## MySQL 兼容性

* 与 MySQL 类似，`USAGE` 权限表示登录 TiDB 服务器的能力。
* 目前不支持列级权限。
* 与 MySQL 类似，不存在 `NO_AUTO_CREATE_USER` sql 模式时，`GRANT` 语句将在用户不存在时自动创建一个空密码的新用户。删除此 sql-mode（默认情况下已启用）会带来安全风险。

## 另请参阅

* [`GRANT <role>`](/sql-statements/sql-statement-grant-role.md)
* [`REVOKE <privileges>`](/sql-statements/sql-statement-revoke-privileges.md)
* [`SHOW GRANTS`](/sql-statements/sql-statement-show-grants.md)
* [权限管理](/privilege-management.md)
