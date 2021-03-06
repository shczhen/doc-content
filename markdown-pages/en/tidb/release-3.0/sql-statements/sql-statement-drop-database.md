---
title: DROP DATABASE | TiDB SQL Statement Reference
summary: An overview of the usage of DROP DATABASE for the TiDB database.
aliases: ['/docs/v3.0/sql-statements/sql-statement-drop-database/','/docs/v3.0/reference/sql/statements/drop-database/']
---

# DROP DATABASE

The `DROP DATABASE` statement permanently removes a specified database schema, and all of the tables and views that were created inside. User privileges that are associated with the dropped database remain unaffected.

## Synopsis

**DropDatabaseStmt:**

![DropDatabaseStmt](https://download.pingcap.com/images/docs/sqlgram/DropDatabaseStmt.png)

**DatabaseSym:**

![DatabaseSym](https://download.pingcap.com/images/docs/sqlgram/DatabaseSym.png)

**IfExists:**

![IfExists](https://download.pingcap.com/images/docs/sqlgram/IfExists.png)

**DBName:**

![DBName](https://download.pingcap.com/images/docs/sqlgram/DBName.png)

## Examples

```sql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| INFORMATION_SCHEMA |
| PERFORMANCE_SCHEMA |
| mysql              |
| test               |
+--------------------+
4 rows in set (0.00 sec)

mysql> DROP DATABASE test;
Query OK, 0 rows affected (0.25 sec)

mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| INFORMATION_SCHEMA |
| PERFORMANCE_SCHEMA |
| mysql              |
+--------------------+
3 rows in set (0.00 sec)
```

## MySQL compatibility

This statement is understood to be fully compatible with MySQL. Any compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [CREATE DATABASE](/sql-statements/sql-statement-create-database.md)
* [ALTER DATABASE](/sql-statements/sql-statement-alter-database.md)
