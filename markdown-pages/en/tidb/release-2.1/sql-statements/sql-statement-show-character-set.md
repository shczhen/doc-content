---
title: SHOW CHARACTER SET | TiDB SQL Statement Reference
summary: An overview of the usage of SHOW CHARACTER SET for the TiDB database.
aliases: ['/docs/v2.1/sql-statements/sql-statement-show-character-set/','/docs/v2.1/reference/sql/statements/show-character-set/']
---

# SHOW CHARACTER SET

This statement provides a static list of available character sets in TiDB. The output does not reflect any attributes of the current connection or user.

## Synopsis

**ShowStmt:**

![ShowStmt](https://download.pingcap.com/images/docs/sqlgram/ShowStmt.png)

**ShowTargetFilterable:**

![ShowTargetFilterable](https://download.pingcap.com/images/docs/sqlgram/ShowTargetFilterable.png)

**CharsetKw:**

![CharsetKw](https://download.pingcap.com/images/docs/sqlgram/CharsetKw.png)

## Examples

```sql
mysql> SHOW CHARACTER SET;
+---------+---------------+-------------------+--------+
| Charset | Description   | Default collation | Maxlen |
+---------+---------------+-------------------+--------+
| utf8    | UTF-8 Unicode | utf8_bin          |      3 |
| utf8mb4 | UTF-8 Unicode | utf8mb4_bin       |      4 |
| ascii   | US ASCII      | ascii_bin         |      1 |
| latin1  | Latin1        | latin1_bin        |      1 |
| binary  | binary        | binary            |      1 |
+---------+---------------+-------------------+--------+
5 rows in set (0.00 sec)
```

## MySQL compatibility

The usage of this statement is understood to be fully compatible with MySQL. However, charsets in TiDB may have different default collations compared with MySQL. For details, refer to [Compatibility with MySQL](/mysql-compatibility.md). Any other compatibility differences should be [reported via an issue](https://github.com/pingcap/tidb/issues/new/choose) on GitHub.

## See also

* [SHOW COLLATION](/sql-statements/sql-statement-show-collation.md)
