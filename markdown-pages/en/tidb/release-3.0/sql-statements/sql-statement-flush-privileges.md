---
title: FLUSH PRIVILEGES | TiDB SQL Statement Reference
summary: An overview of the usage of FLUSH PRIVILEGES for the TiDB database.
aliases: ['/docs/v3.0/sql-statements/sql-statement-flush-privileges/','/docs/v3.0/reference/sql/statements/flush-privileges/']
---

# FLUSH PRIVILEGES

This statement triggers TiDB to reload the in-memory copy of privileges from the privilege tables. You should execute `FLUSH PRIVILEGES` after making manual edits to tables such as `mysql.user`. Executing this statement is not required after using privilege statements such as `GRANT` or `REVOKE`.

## Synopsis

**FlushStmt:**

![FlushStmt](https://download.pingcap.com/images/docs/sqlgram/FlushStmt.png)

**NoWriteToBinLogAliasOpt:**

![NoWriteToBinLogAliasOpt](https://download.pingcap.com/images/docs/sqlgram/NoWriteToBinLogAliasOpt.png)

**FlushOption:**

![FlushOption](https://download.pingcap.com/images/docs/sqlgram/FlushOption.png)

## Examples

```sql
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)
```

## MySQL compatibility

This statement is understood to be fully compatible with MySQL. Any compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [Privilege Management](/privilege-management.md)
