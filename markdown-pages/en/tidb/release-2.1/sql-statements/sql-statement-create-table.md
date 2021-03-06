---
title: CREATE TABLE | TiDB SQL Statement Reference
summary: An overview of the usage of CREATE TABLE for the TiDB database.
aliases: ['/docs/v2.1/sql-statements/sql-statement-create-table/','/docs/v2.1/reference/sql/statements/create-table/']
---

# CREATE TABLE

This statement creates a new table in the currently selected database. It behaves similarly to the `CREATE TABLE` statement in MySQL.

## Synopsis

**CreateTableStmt:**

![CreateTableStmt](https://download.pingcap.com/images/docs/sqlgram/CreateTableStmt.png)

**IfNotExists:**

![IfNotExists](https://download.pingcap.com/images/docs/sqlgram/IfNotExists.png)

**TableName:**

![TableName](https://download.pingcap.com/images/docs/sqlgram/TableName.png)

**TableElementListOpt:**

![TableElementListOpt](https://download.pingcap.com/images/docs/sqlgram/TableElementListOpt.png)

**TableElement:**

![TableElement](https://download.pingcap.com/images/docs/sqlgram/TableElement.png)

**PartitionOpt:**

![PartitionOpt](https://download.pingcap.com/images/docs/sqlgram/PartitionOpt.png)

**ColumnDef:**

![ColumnDef](https://download.pingcap.com/images/docs/sqlgram/ColumnDef.png)

**ColumnName:**

![ColumnName](https://download.pingcap.com/images/docs/sqlgram/ColumnName.png)

**Type:**

![Type](https://download.pingcap.com/images/docs/sqlgram/Type.png)

**ColumnOptionListOpt:**

![ColumnOptionListOpt](https://download.pingcap.com/images/docs/sqlgram/ColumnOptionListOpt.png)

**TableOptionListOpt:**

![TableOptionListOpt](https://download.pingcap.com/images/docs/sqlgram/TableOptionListOpt.png)

The following *table_options* are supported. Other options such as `AVG_ROW_LENGTH`, `CHECKSUM`, `COMPRESSION`, `CONNECTION`, `DELAY_KEY_WRITE`, `ENGINE`, `KEY_BLOCK_SIZE`, `MAX_ROWS`, `MIN_ROWS`, `ROW_FORMAT` and `STATS_PERSISTENT` are parsed but ignored.

| Options | Description | Example |
| ---------- | ---------- | ------- |
| `AUTO_INCREMENT` | The initial value of the increment field | `AUTO_INCREMENT` = 5 |
| `SHARD_ROW_ID_BITS`| To set the number of bits for the implicit `_tidb_rowid` shards |`SHARD_ROW_ID_BITS` = 4|
|`PRE_SPLIT_REGIONS`| To pre-split `2^(PRE_SPLIT_REGIONS)` Regions when creating a table |`PRE_SPLIT_REGIONS` = 4|
| `CHARACTER SET` | To specify the [character set](/character-set-and-collation.md) for the table | `CHARACTER SET` =  'utf8mb4' |
| `COMMENT` | The comment information | `COMMENT` = 'comment info' |

> **Note:**
>
> In TiDB 2.1 versions, the three features `SHARD_ROW_ID_BITS`, `PRE_SPLIT_REGIONS` and `COLLATE` are supported starting from the 2.1.13 version (including 2.1.13).

## Examples

Creating a simple table and inserting one row:


```sql
CREATE TABLE t1 (a int);
DESC t1;
SHOW CREATE TABLE t1\G
INSERT INTO t1 (a) VALUES (1);
SELECT * FROM t1;
```

```
mysql> drop table if exists t1;
Query OK, 0 rows affected (0.23 sec)

mysql> CREATE TABLE t1 (a int);
Query OK, 0 rows affected (0.09 sec)

mysql> DESC t1;
+-------+---------+------+------+---------+-------+
| Field | Type    | Null | Key  | Default | Extra |
+-------+---------+------+------+---------+-------+
| a     | int(11) | YES  |      | NULL    |       |
+-------+---------+------+------+---------+-------+
1 row in set (0.00 sec)

mysql> SHOW CREATE TABLE t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `a` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
1 row in set (0.00 sec)

mysql> INSERT INTO t1 (a) VALUES (1);
Query OK, 1 row affected (0.03 sec)

mysql> SELECT * FROM t1;
+------+
| a    |
+------+
|    1 |
+------+
1 row in set (0.00 sec)
```

Dropping a table if it exists, and conditionally creating a table if it does not exist:


```sql
DROP TABLE IF EXISTS t1;
CREATE TABLE IF NOT EXISTS t1 (
 id BIGINT NOT NULL PRIMARY KEY auto_increment,
 b VARCHAR(200) NOT NULL
);
DESC t1;
```

```sql
mysql> DROP TABLE IF EXISTS t1;
Query OK, 0 rows affected (0.22 sec)

mysql> CREATE TABLE IF NOT EXISTS t1 (
    ->  id BIGINT NOT NULL PRIMARY KEY auto_increment,
    ->  b VARCHAR(200) NOT NULL
    -> );
Query OK, 0 rows affected (0.08 sec)

mysql> DESC t1;
+-------+--------------+------+------+---------+----------------+
| Field | Type         | Null | Key  | Default | Extra          |
+-------+--------------+------+------+---------+----------------+
| id    | bigint(20)   | NO   | PRI  | NULL    | auto_increment |
| b     | varchar(200) | NO   |      | NULL    |                |
+-------+--------------+------+------+---------+----------------+
2 rows in set (0.00 sec)
```

## MySQL compatibility

* TiDB does not support the syntax `CREATE TEMPORARY TABLE`.
* All of the data types except spatial types are supported.
* `FULLTEXT`, `HASH` and `SPATIAL` indexes are not supported.
* The `[ASC | DESC]` in `index_col_name` is currently parsed but ignored (MySQL 5.7 compatible behavior).
* The `COMMENT` attribute supports a maximum of 1024 characters and does not support the `WITH PARSER` option.
* TiDB supports at most 512 columns in a single table. The corresponding number limit in InnoDB is 1017, and the hard limit in MySQL is 4096.
* `CHECK` constraints are parsed but ignored (MySQL 5.7 compatible behavior). For details, see [Constraints](/constraints.md).
* `FOREIGN KEY` constraints are parsed and stored, but not enforced by DML statements. For details, see [Constraints](/constraints.md).

## See also

* [Data Types](/data-type-overview.md)
* [DROP TABLE](/sql-statements/sql-statement-drop-table.md)
* [CREATE TABLE LIKE](/sql-statements/sql-statement-create-table-like.md)
* [SHOW CREATE TABLE](/sql-statements/sql-statement-show-create-table.md)
