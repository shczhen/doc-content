---
title: CREATE DATABASE
summary: TiDB 数据库中 CREATE DATABASE 的使用概况。
aliases: ['/docs-cn/v2.1/sql-statements/sql-statement-create-database/','/docs-cn/v2.1/reference/sql/statements/create-database/']
---

# CREATE DATABASE

`CREATE DATABASE` 语句用于在 TiDB 上创建新数据库。按照 SQL 标准，“数据库” 一词在 MySQL 术语中最接近 “schema”。

## 语法图

**CreateDatabaseStmt:**

![CreateDatabaseStmt](https://download.pingcap.com/images/docs-cn/sqlgram/CreateDatabaseStmt.png)

**DatabaseSym:**

![DatabaseSym](https://download.pingcap.com/images/docs-cn/sqlgram/DatabaseSym.png)

**IfNotExists:**

![IfNotExists](https://download.pingcap.com/images/docs-cn/sqlgram/IfNotExists.png)

**DBName:**

![DBName](https://download.pingcap.com/images/docs-cn/sqlgram/DBName.png)

**DatabaseOptionListOpt:**

![DatabaseOptionListOpt](https://download.pingcap.com/images/docs-cn/sqlgram/DatabaseOptionListOpt.png)

## 语法说明

`CREATE DATABASE` 用于创建数据库，并可以指定数据库的默认属性（如数据库默认字符集、校验规则。`CREATE SCHEMA` 跟 `CREATE DATABASE` 操作效果一样。

```sql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_specification] ...

create_specification:
    [DEFAULT] CHARACTER SET [=] charset_name
  | [DEFAULT] COLLATE [=] collation_name
```

当创建已存在的数据库且不指定使用 `IF NOT EXISTS` 时会报错。

`create_specification` 选项用于指定数据库具体的 `CHARACTER SET` 和 `COLLATE`。目前这个选项只是语法支持。

## 示例

```sql
mysql> CREATE DATABASE mynewdatabase;
Query OK, 0 rows affected (0.09 sec)

mysql> USE mynewdatabase;
Database changed
mysql> CREATE TABLE t1 (a int);
Query OK, 0 rows affected (0.11 sec)

mysql> SHOW TABLES;
+-------------------------+
| Tables_in_mynewdatabase |
+-------------------------+
| t1                      |
+-------------------------+
1 row in set (0.00 sec)
```

## MySQL 兼容性

`CREATE DATABASE` 语句与 MySQL 完全兼容。如发现任何兼容性差异，请在 GitHub 上提交 [issue](https://github.com/pingcap/tidb/issues/new/choose)。

## 另请参阅

* [USE](/sql-statements/sql-statement-use.md)
* [ALTER DATABASE](/sql-statements/sql-statement-alter-database.md)
* [DROP DATABASE](/sql-statements/sql-statement-drop-database.md)
* [SHOW DATABASES](/sql-statements/sql-statement-show-databases.md)
