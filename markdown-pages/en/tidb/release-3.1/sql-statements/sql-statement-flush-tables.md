---
title: FLUSH TABLES | TiDB SQL Statement Reference
summary: An overview of the usage of FLUSH TABLES for the TiDB database.
aliases: ['/docs/v3.1/sql-statements/sql-statement-flush-tables/','/docs/v3.1/reference/sql/statements/flush-tables/']
---

# FLUSH TABLES

This statement is included for compatibility with MySQL. It has no effective usage in TiDB.

## Synopsis

**FlushStmt:**

![FlushStmt](https://download.pingcap.com/images/docs/sqlgram/FlushStmt.png)

**NoWriteToBinLogAliasOpt:**

![NoWriteToBinLogAliasOpt](https://download.pingcap.com/images/docs/sqlgram/NoWriteToBinLogAliasOpt.png)

**FlushOption:**

![FlushOption](https://download.pingcap.com/images/docs/sqlgram/FlushOption.png)

**TableOrTables:**

![TableOrTables](https://download.pingcap.com/images/docs/sqlgram/TableOrTables.png)

**TableNameListOpt:**

![TableNameListOpt](https://download.pingcap.com/images/docs/sqlgram/TableNameListOpt.png)

**WithReadLockOpt:**

![WithReadLockOpt](https://download.pingcap.com/images/docs/sqlgram/WithReadLockOpt.png)

## Examples

```sql
mysql> FLUSH TABLES;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH TABLES WITH READ LOCK;
ERROR 1105 (HY000): FLUSH TABLES WITH READ LOCK is not supported.  Please use @@tidb_snapshot
```

## MySQL compatibility

* TiDB does not have a concept of table cache as in MySQL. Thus, `FLUSH TABLES` is parsed but ignored in TiDB for compatibility.
* The statement `FLUSH TABLES WITH READ LOCK` produces an error, as TiDB does not currently support locking tables. It is recommended to use [Historical reads](/read-historical-data.md) for this purpose instead.

## See also

* [Read historical data](/read-historical-data.md)
