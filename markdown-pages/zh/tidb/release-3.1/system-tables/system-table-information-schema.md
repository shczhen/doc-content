---
title: Information Schema
aliases: ['/docs-cn/v3.1/system-tables/system-table-information-schema/','/docs-cn/v3.1/reference/system-databases/information-schema/']
---

# Information Schema

为了和 MySQL 保持兼容，TiDB 支持很多 `INFORMATION_SCHEMA` 表，其中有不少表都支持相应的 `SHOW` 命令。查询 `INFORMATION_SCHEMA` 表也为表的连接操作提供了可能。

## ANALYZE_STATUS 表

`ANALYZE_STATUS` 表提供正在执行的收集统计信息的任务以及有限条历史任务记录。


```sql
select * from `ANALYZE_STATUS`;
```

```
+--------------+------------+----------------+-------------------+----------------+---------------------+----------+
| TABLE_SCHEMA | TABLE_NAME | PARTITION_NAME | JOB_INFO          | PROCESSED_ROWS | START_TIME          | STATE    |
+--------------+------------+----------------+-------------------+----------------+---------------------+----------+
| test         | t          |                | analyze index idx | 2              | 2019-06-21 19:51:14 | finished |
| test         | t          |                | analyze columns   | 2              | 2019-06-21 19:51:14 | finished |
| test         | t1         | p0             | analyze columns   | 0              | 2019-06-21 19:51:15 | finished |
| test         | t1         | p3             | analyze columns   | 0              | 2019-06-21 19:51:15 | finished |
| test         | t1         | p1             | analyze columns   | 0              | 2019-06-21 19:51:15 | finished |
| test         | t1         | p2             | analyze columns   | 1              | 2019-06-21 19:51:15 | finished |
+--------------+------------+----------------+-------------------+----------------+---------------------+----------+
6 rows in set
```

## CHARACTER_SETS 表

`CHARACTER_SETS` 表提供[字符集](/character-set-and-collation.md)相关的信息。TiDB 目前仅支持部分字符集。


```sql
SELECT * FROM character_sets;
```

```
+--------------------+----------------------+---------------+--------+
| CHARACTER_SET_NAME | DEFAULT_COLLATE_NAME | DESCRIPTION   | MAXLEN |
+--------------------+----------------------+---------------+--------+
| utf8               | utf8_bin             | UTF-8 Unicode |      3 |
| utf8mb4            | utf8mb4_bin          | UTF-8 Unicode |      4 |
| ascii              | ascii_bin            | US ASCII      |      1 |
| latin1             | latin1_bin           | Latin1        |      1 |
| binary             | binary               | binary        |      1 |
+--------------------+----------------------+---------------+--------+
5 rows in set (0.00 sec)
```

## COLLATIONS 表

`COLLATIONS` 表提供了 `CHARACTER_SETS` 表中字符集对应的排序规则列表。TiDB 当前仅支持二进制排序规则，包含该表仅为兼容 MySQL。


```sql
SELECT * FROM collations WHERE character_set_name='utf8mb4';
```

```
+------------------------+--------------------+------+------------+-------------+---------+
| COLLATION_NAME         | CHARACTER_SET_NAME | ID   | IS_DEFAULT | IS_COMPILED | SORTLEN |
+------------------------+--------------------+------+------------+-------------+---------+
| utf8mb4_general_ci     | utf8mb4            |   45 | Yes        | Yes         |       1 |
| utf8mb4_bin            | utf8mb4            |   46 |            | Yes         |       1 |
| utf8mb4_unicode_ci     | utf8mb4            |  224 |            | Yes         |       1 |
| utf8mb4_icelandic_ci   | utf8mb4            |  225 |            | Yes         |       1 |
| utf8mb4_latvian_ci     | utf8mb4            |  226 |            | Yes         |       1 |
| utf8mb4_romanian_ci    | utf8mb4            |  227 |            | Yes         |       1 |
| utf8mb4_slovenian_ci   | utf8mb4            |  228 |            | Yes         |       1 |
| utf8mb4_polish_ci      | utf8mb4            |  229 |            | Yes         |       1 |
| utf8mb4_estonian_ci    | utf8mb4            |  230 |            | Yes         |       1 |
| utf8mb4_spanish_ci     | utf8mb4            |  231 |            | Yes         |       1 |
| utf8mb4_swedish_ci     | utf8mb4            |  232 |            | Yes         |       1 |
| utf8mb4_turkish_ci     | utf8mb4            |  233 |            | Yes         |       1 |
| utf8mb4_czech_ci       | utf8mb4            |  234 |            | Yes         |       1 |
| utf8mb4_danish_ci      | utf8mb4            |  235 |            | Yes         |       1 |
| utf8mb4_lithuanian_ci  | utf8mb4            |  236 |            | Yes         |       1 |
| utf8mb4_slovak_ci      | utf8mb4            |  237 |            | Yes         |       1 |
| utf8mb4_spanish2_ci    | utf8mb4            |  238 |            | Yes         |       1 |
| utf8mb4_roman_ci       | utf8mb4            |  239 |            | Yes         |       1 |
| utf8mb4_persian_ci     | utf8mb4            |  240 |            | Yes         |       1 |
| utf8mb4_esperanto_ci   | utf8mb4            |  241 |            | Yes         |       1 |
| utf8mb4_hungarian_ci   | utf8mb4            |  242 |            | Yes         |       1 |
| utf8mb4_sinhala_ci     | utf8mb4            |  243 |            | Yes         |       1 |
| utf8mb4_german2_ci     | utf8mb4            |  244 |            | Yes         |       1 |
| utf8mb4_croatian_ci    | utf8mb4            |  245 |            | Yes         |       1 |
| utf8mb4_unicode_520_ci | utf8mb4            |  246 |            | Yes         |       1 |
| utf8mb4_vietnamese_ci  | utf8mb4            |  247 |            | Yes         |       1 |
+------------------------+--------------------+------+------------+-------------+---------+
26 rows in set (0.00 sec)
```

## COLLATION_CHARACTER_SET_APPLICABILITY 表

`COLLATION_CHARACTER_SET_APPLICABILITY` 表将排序规则映射至适用的字符集名称。和 `COLLATIONS` 表一样，包含此表也是为了兼容 MySQL。


```sql
SELECT * FROM collation_character_set_applicability WHERE character_set_name='utf8mb4';
```

```
+------------------------+--------------------+
| COLLATION_NAME         | CHARACTER_SET_NAME |
+------------------------+--------------------+
| utf8mb4_general_ci     | utf8mb4            |
| utf8mb4_bin            | utf8mb4            |
| utf8mb4_unicode_ci     | utf8mb4            |
| utf8mb4_icelandic_ci   | utf8mb4            |
| utf8mb4_latvian_ci     | utf8mb4            |
| utf8mb4_romanian_ci    | utf8mb4            |
| utf8mb4_slovenian_ci   | utf8mb4            |
| utf8mb4_polish_ci      | utf8mb4            |
| utf8mb4_estonian_ci    | utf8mb4            |
| utf8mb4_spanish_ci     | utf8mb4            |
| utf8mb4_swedish_ci     | utf8mb4            |
| utf8mb4_turkish_ci     | utf8mb4            |
| utf8mb4_czech_ci       | utf8mb4            |
| utf8mb4_danish_ci      | utf8mb4            |
| utf8mb4_lithuanian_ci  | utf8mb4            |
| utf8mb4_slovak_ci      | utf8mb4            |
| utf8mb4_spanish2_ci    | utf8mb4            |
| utf8mb4_roman_ci       | utf8mb4            |
| utf8mb4_persian_ci     | utf8mb4            |
| utf8mb4_esperanto_ci   | utf8mb4            |
| utf8mb4_hungarian_ci   | utf8mb4            |
| utf8mb4_sinhala_ci     | utf8mb4            |
| utf8mb4_german2_ci     | utf8mb4            |
| utf8mb4_croatian_ci    | utf8mb4            |
| utf8mb4_unicode_520_ci | utf8mb4            |
| utf8mb4_vietnamese_ci  | utf8mb4            |
+------------------------+--------------------+
26 rows in set (0.00 sec)
```

## COLUMNS 表

`COLUMNS` 表提供了表的所有列的信息。


```sql
CREATE TABLE test.t1 (a int);
```

```
1 row in set (0.01 sec)
```


```sql
SELECT * FROM information_schema.columns WHERE table_schema='test' AND TABLE_NAME='t1';
```

```
*************************** 1. row ***************************
           TABLE_CATALOG: def
            TABLE_SCHEMA: test
              TABLE_NAME: t1
             COLUMN_NAME: a
        ORDINAL_POSITION: 1
          COLUMN_DEFAULT: NULL
             IS_NULLABLE: YES
               DATA_TYPE: int
CHARACTER_MAXIMUM_LENGTH: NULL
  CHARACTER_OCTET_LENGTH: NULL
       NUMERIC_PRECISION: 11
           NUMERIC_SCALE: 0
      DATETIME_PRECISION: NULL
      CHARACTER_SET_NAME: NULL
          COLLATION_NAME: NULL
             COLUMN_TYPE: int(11)
              COLUMN_KEY:
                   EXTRA:
              PRIVILEGES: select,insert,update,references
          COLUMN_COMMENT:
   GENERATION_EXPRESSION:
1 row in set (0.01 sec)
```

`COLUMNS` 表中列的含义如下：

* `TABLE_CATALOG`：包含列的表所属的目录的名称。该值始终为 `def`。
* `TABLE_SCHEMA`：包含列的表所属的数据库的名称。
* `TABLE_NAME`：包含列的表的名称。
* `COLUMN_NAME`：列的名称。
* `ORDINAL_POSITION`：表中列的位置。
* `COLUMN_DEFAULT`：列的默认值。如果列的显式默认值为 `NULL`，或者列定义中不包含 `default` 子句，则此值为 `NULL`。
* `IS_NULLABLE`：列的可空性。如果列中可以存储空值，则该值为 `YES`，则为 `NO`。
* `DATA_TYPE`：列数据类型。
* `CHARACTER_MAXIMUM_LENGTH`：对于字符串列，以字符为单位的最大长度。
* `CHARACTER_OCTET_LENGTH`：对于字符串列，以字节为单位的最大长度。
* `NUMERIC_PRECISION`：对于数字列，为数字精度。
* `NUMERIC_SCALE`：对于数字列，为数字刻度。
* `DATETIME_PRECISION`：对于时间列，小数秒精度。
* `CHARACTER_SET_NAME`：对于字符串列，字符集名称。
* `COLLATION_NAME`：对于字符串列，排序规则名称。
* `COLUMN_TYPE`：列类型。
* `COLUMN_KEY`：该列是否被索引。具体显示如下:
    * 如果此值为空，则该列要么未被索引，要么被索引且是多列非唯一索引中的第二列。
    * 如果此值是`PRI`，则该列是主键，或者是多列主键中的一列。
    * 如果此值是`UNI`，则该列是唯一索引的第一列。
    * 如果此值是`MUL`，则该列是非唯一索引的第一列，在该列中允许给定值的多次出现。
* `EXTRA`：关于给定列的任何附加信息。
* `PRIVILEGES`：当前用户对该列拥有的权限。目前在 TiDB 中，此值为定值，一直为 `select,insert,update,references`。
* `COLUMN_COMMENT`：列定义中包含的注释。
* `GENERATION_EXPRESSION`：对于生成的列，显示用于计算列值的表达式。对于未生成的列为空。

对应的 `SHOW` 语句如下：


```sql
SHOW COLUMNS FROM t1 FROM test;
```

```
+-------+---------+------+------+---------+-------+
| Field | Type    | Null | Key  | Default | Extra |
+-------+---------+------+------+---------+-------+
| a     | int(11) | YES  |      | NULL    |       |
+-------+---------+------+------+---------+-------+
1 row in set (0.00 sec)
```

## ENGINES 表

`ENGINES` 表提供了关于存储引擎的信息。从和 MySQL 兼容性上考虑，TiDB 会一直将 InnoDB 描述为唯一支持的引擎。此外，`ENGINES` 表中其它列值也都是定值。


```sql
SELECT * FROM engines;
```

```
*************************** 1. row ***************************
      ENGINE: InnoDB
     SUPPORT: DEFAULT
     COMMENT: Supports transactions, row-level locking, and foreign keys
TRANSACTIONS: YES
          XA: YES
  SAVEPOINTS: YES
1 row in set (0.00 sec)
```

`ENGINES` 表中列的含义如下：

* `ENGINE`：存储引擎的名称。
* `SUPPORT`：服务器对存储引擎的支持级别，在 TiDB 中此值一直是 `DEFAULT`。
* `COMMENT`：存储引擎的简要描述。
* `TRANSACTIONS`：存储引擎是否支持事务。
* `XA`：存储引擎是否支持 XA 事务。
* `SAVEPOINTS`：存储引擎是否支持 `savepoints`。

## KEY_COLUMN_USAGE 表

`KEY_COLUMN_USAGE` 表描述了列的键约束，比如主键约束。


```sql
SELECT * FROM key_column_usage WHERE table_schema='mysql' and table_name='user';
```

```
*************************** 1. row ***************************
           CONSTRAINT_CATALOG: def
            CONSTRAINT_SCHEMA: mysql
              CONSTRAINT_NAME: PRIMARY
                TABLE_CATALOG: def
                 TABLE_SCHEMA: mysql
                   TABLE_NAME: user
                  COLUMN_NAME: Host
             ORDINAL_POSITION: 1
POSITION_IN_UNIQUE_CONSTRAINT: NULL
      REFERENCED_TABLE_SCHEMA: NULL
        REFERENCED_TABLE_NAME: NULL
       REFERENCED_COLUMN_NAME: NULL
*************************** 2. row ***************************
           CONSTRAINT_CATALOG: def
            CONSTRAINT_SCHEMA: mysql
              CONSTRAINT_NAME: PRIMARY
                TABLE_CATALOG: def
                 TABLE_SCHEMA: mysql
                   TABLE_NAME: user
                  COLUMN_NAME: User
             ORDINAL_POSITION: 2
POSITION_IN_UNIQUE_CONSTRAINT: NULL
      REFERENCED_TABLE_SCHEMA: NULL
        REFERENCED_TABLE_NAME: NULL
       REFERENCED_COLUMN_NAME: NULL
2 rows in set (0.00 sec)
```

`KEY_COLUMN_USAGE` 表中列的含义如下：

* `CONSTRAINT_CATALOG`：约束所属的目录的名称。该值始终为 `def`。
* `CONSTRAINT_SCHEMA`：约束所属的数据库的名称。
* `CONSTRAINT_NAME`：约束名称。
* `TABLE_CATALOG`：表所属目录的名称。该值始终为 `def`。
* `TABLE_SCHEMA`：表所属的架构数据库的名称。
* `TABLE_NAME`：具有约束的表的名称。
* `COLUMN_NAME`：具有约束的列的名称。
* `ORDINAL_POSITION`：列在约束中的位置，而不是列在表中的位置。列位置从 1 开始编号。
* `POSITION_IN_UNIQUE_CONSTRAINT`：唯一约束和主键约束为空。对于外键约束，此列是被引用的表的键的序号位置。
* `REFERENCED_TABLE_SCHEMA`：约束引用的数据库的名称。目前在 TiDB 中，除了外键约束，其它约束此列的值都为 `nil`。
* `REFERENCED_TABLE_NAME`：约束引用的表的名称。目前在 TiDB 中，除了外键约束，其它约束此列的值都为 `nil`。
* `REFERENCED_COLUMN_NAME`：约束引用的列的名称。目前在 TiDB 中，除了外键约束，其它约束此列的值都为 `nil`。

## PROCESSLIST 表

`PROCESSLIST` 和 `show processlist` 的功能一样，都是查看当前正在处理的请求。

`PROCESSLIST` 表会比 `show processlist` 多一个 `MEM` 列，`MEM` 是指正在处理的请求已使用的内存，单位是 byte。

```sql
+----+------+------+--------------------+---------+------+-------+---------------------------+-----+
| ID | USER | HOST | DB                 | COMMAND | TIME | STATE | INFO                      | MEM |
+----+------+------+--------------------+---------+------+-------+---------------------------+-----+
| 1  | root | ::1  | INFORMATION_SCHEMA | Query   | 0    | 2     | select * from PROCESSLIST | 0   |
+----+------+------+--------------------+---------+------+-------+---------------------------+-----+
```

## SCHEMATA 表

`SCHEMATA` 表提供了关于数据库的信息。表中的数据与 `SHOW DATABASES` 语句的执行结果等价。


```sql
SELECT * FROM schemata;
```

```
+--------------+--------------------+----------------------------+------------------------+----------+
| CATALOG_NAME | SCHEMA_NAME        | DEFAULT_CHARACTER_SET_NAME | DEFAULT_COLLATION_NAME | SQL_PATH |
+--------------+--------------------+----------------------------+------------------------+----------+
| def          | INFORMATION_SCHEMA | utf8mb4                    | utf8mb4_bin            | NULL     |
| def          | mynewdb            | utf8mb4                    | utf8mb4_bin            | NULL     |
| def          | mysql              | utf8mb4                    | utf8mb4_bin            | NULL     |
| def          | PERFORMANCE_SCHEMA | utf8mb4                    | utf8mb4_bin            | NULL     |
| def          | test               | utf8mb4                    | utf8mb4_bin            | NULL     |
+--------------+--------------------+----------------------------+------------------------+----------+
5 rows in set (0.00 sec)
```

## SESSION_VARIABLES 表

`SESSION_VARIABLES` 表提供了关于 session 变量的信息。表中的数据跟 `SHOW SESSION VARIABLES` 语句执行结果类似。


```sql
SELECT * FROM session_variables LIMIT 10;
```

```
+----------------------------------+----------------------+
| VARIABLE_NAME                    | VARIABLE_VALUE       |
+----------------------------------+----------------------+
| max_write_lock_count             | 18446744073709551615 |
| server_id_bits                   | 32                   |
| net_read_timeout                 | 30                   |
| innodb_online_alter_log_max_size | 134217728            |
| innodb_optimize_fulltext_only    | OFF                  |
| max_join_size                    | 18446744073709551615 |
| innodb_read_io_threads           | 4                    |
| session_track_gtids              | OFF                  |
| have_ssl                         | DISABLED             |
| max_binlog_cache_size            | 18446744073709547520 |
+----------------------------------+----------------------+
10 rows in set (0.00 sec)
```

## SLOW_QUERY 表

`SLOW_QUERY` 表中提供了慢查询相关的信息，其内容通过解析 TiDB 慢查询日志而来，列名和慢日志中的字段名是一一对应。关于如何使用该表调查和改善慢查询请参考[慢查询日志文档](/identify-slow-queries.md)。

```sql
mysql> desc information_schema.slow_query;
+---------------+---------------------+------+------+---------+-------+
| Field         | Type                | Null | Key  | Default | Extra |
+---------------+---------------------+------+------+---------+-------+
| Time          | timestamp unsigned  | YES  |      | NULL    |       |
| Txn_start_ts  | bigint(20) unsigned | YES  |      | NULL    |       |
| User          | varchar(64)         | YES  |      | NULL    |       |
| Host          | varchar(64)         | YES  |      | NULL    |       |
| Conn_ID       | bigint(20) unsigned | YES  |      | NULL    |       |
| Query_time    | double unsigned     | YES  |      | NULL    |       |
| Process_time  | double unsigned     | YES  |      | NULL    |       |
| Wait_time     | double unsigned     | YES  |      | NULL    |       |
| Backoff_time  | double unsigned     | YES  |      | NULL    |       |
| Request_count | bigint(20) unsigned | YES  |      | NULL    |       |
| Total_keys    | bigint(20) unsigned | YES  |      | NULL    |       |
| Process_keys  | bigint(20) unsigned | YES  |      | NULL    |       |
| DB            | varchar(64)         | YES  |      | NULL    |       |
| Index_ids     | varchar(100)        | YES  |      | NULL    |       |
| Is_internal   | tinyint(1) unsigned | YES  |      | NULL    |       |
| Digest        | varchar(64)         | YES  |      | NULL    |       |
| Stats         | varchar(512)        | YES  |      | NULL    |       |
| Cop_proc_avg  | double unsigned     | YES  |      | NULL    |       |
| Cop_proc_p90  | double unsigned     | YES  |      | NULL    |       |
| Cop_proc_max  | double unsigned     | YES  |      | NULL    |       |
| Cop_proc_addr | varchar(64)         | YES  |      | NULL    |       |
| Cop_wait_avg  | double unsigned     | YES  |      | NULL    |       |
| Cop_wait_p90  | double unsigned     | YES  |      | NULL    |       |
| Cop_wait_max  | double unsigned     | YES  |      | NULL    |       |
| Cop_wait_addr | varchar(64)         | YES  |      | NULL    |       |
| Mem_max       | bigint(20) unsigned | YES  |      | NULL    |       |
| Succ          | tinyint(1) unsigned | YES  |      | NULL    |       |
| Query         | longblob unsigned   | YES  |      | NULL    |       |
+---------------+---------------------+------+------+---------+-------+
```

## STATISTICS 表

`STATISTICS` 表提供了关于表索引的信息。


```sql
desc statistics;
```

```
+---------------|---------------------|------|------|---------|-------+
| Field         | Type                | Null | Key  | Default | Extra |
+---------------|---------------------|------|------|---------|-------+
| TABLE_CATALOG | varchar(512)        | YES  |      | NULL    |       |
| TABLE_SCHEMA  | varchar(64)         | YES  |      | NULL    |       |
| TABLE_NAME    | varchar(64)         | YES  |      | NULL    |       |
| NON_UNIQUE    | varchar(1)          | YES  |      | NULL    |       |
| INDEX_SCHEMA  | varchar(64)         | YES  |      | NULL    |       |
| INDEX_NAME    | varchar(64)         | YES  |      | NULL    |       |
| SEQ_IN_INDEX  | bigint(2) UNSIGNED  | YES  |      | NULL    |       |
| COLUMN_NAME   | varchar(21)         | YES  |      | NULL    |       |
| COLLATION     | varchar(1)          | YES  |      | NULL    |       |
| CARDINALITY   | bigint(21) UNSIGNED | YES  |      | NULL    |       |
| SUB_PART      | bigint(3) UNSIGNED  | YES  |      | NULL    |       |
| PACKED        | varchar(10)         | YES  |      | NULL    |       |
| NULLABLE      | varchar(3)          | YES  |      | NULL    |       |
| INDEX_TYPE    | varchar(16)         | YES  |      | NULL    |       |
| COMMENT       | varchar(16)         | YES  |      | NULL    |       |
| INDEX_COMMENT | varchar(1024)       | YES  |      | NULL    |       |
+---------------|---------------------|------|------|---------|-------+
```

下列语句是等价的：


```sql
SELECT * FROM INFORMATION_SCHEMA.STATISTICS
  WHERE table_name = 'tbl_name'
  AND table_schema = 'db_name'
```


```sql
SHOW INDEX
  FROM tbl_name
  FROM db_name
```

## TABLES 表

`TABLES` 表提供了数据库里面关于表的信息。


```sql
SELECT * FROM tables WHERE table_schema='mysql' AND table_name='user';
```

```
*************************** 1. row ***************************
  TABLE_CATALOG: def
   TABLE_SCHEMA: mysql
     TABLE_NAME: user
     TABLE_TYPE: BASE TABLE
         ENGINE: InnoDB
        VERSION: 10
     ROW_FORMAT: Compact
     TABLE_ROWS: 0
 AVG_ROW_LENGTH: 0
    DATA_LENGTH: 0
MAX_DATA_LENGTH: 0
   INDEX_LENGTH: 0
      DATA_FREE: 0
 AUTO_INCREMENT: 0
    CREATE_TIME: 2019-03-29 09:17:27
    UPDATE_TIME: NULL
     CHECK_TIME: NULL
TABLE_COLLATION: utf8mb4_bin
       CHECKSUM: NULL
 CREATE_OPTIONS:
  TABLE_COMMENT:
  TIDB_TABLE_ID: 5
1 row in set (0.00 sec)
```

以下操作是等价的：


```sql
SELECT table_name FROM INFORMATION_SCHEMA.TABLES
  WHERE table_schema = 'db_name'
  [AND table_name LIKE 'wild']
```


```sql
SHOW TABLES
  FROM db_name
  [LIKE 'wild']
```

## TABLE_CONSTRAINTS 表

`TABLE_CONSTRAINTS` 表记录了表的约束信息。


```sql
SELECT * FROM table_constraints WHERE constraint_type='UNIQUE';
```

```
*************************** 1. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: name
      TABLE_SCHEMA: mysql
        TABLE_NAME: help_topic
   CONSTRAINT_TYPE: UNIQUE
*************************** 2. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: tbl
      TABLE_SCHEMA: mysql
        TABLE_NAME: stats_meta
   CONSTRAINT_TYPE: UNIQUE
*************************** 3. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: tbl
      TABLE_SCHEMA: mysql
        TABLE_NAME: stats_histograms
   CONSTRAINT_TYPE: UNIQUE
*************************** 4. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: tbl
      TABLE_SCHEMA: mysql
        TABLE_NAME: stats_buckets
   CONSTRAINT_TYPE: UNIQUE
*************************** 5. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: delete_range_index
      TABLE_SCHEMA: mysql
        TABLE_NAME: gc_delete_range
   CONSTRAINT_TYPE: UNIQUE
*************************** 6. row ***************************
CONSTRAINT_CATALOG: def
 CONSTRAINT_SCHEMA: mysql
   CONSTRAINT_NAME: delete_range_done_index
      TABLE_SCHEMA: mysql
        TABLE_NAME: gc_delete_range_done
   CONSTRAINT_TYPE: UNIQUE
6 rows in set (0.00 sec)
```

其中：

* `CONSTRAINT_TYPE` 的取值可以是 `UNIQUE`，`PRIMARY KEY`，或者 `FOREIGN KEY`。
* `UNIQUE` 和 `PRIMARY KEY` 信息与 `SHOW INDEX` 语句的执行结果类似。

## TIDB_HOT_REGIONS 表

`TIDB_HOT_REGIONS` 表提供了关于热点 REGION 的相关信息。


```sql
desc TIDB_HOT_REGIONS;
```

```
+----------------+---------------------+------+-----+---------+-------+
| Field          | Type                | Null | Key | Default | Extra |
+----------------+---------------------+------+-----+---------+-------+
| TABLE_ID       | bigint(21) unsigned | YES  |     | <null>  |       |
| INDEX_ID       | bigint(21) unsigned | YES  |     | <null>  |       |
| DB_NAME        | varchar(64)         | YES  |     | <null>  |       |
| TABLE_NAME     | varchar(64)         | YES  |     | <null>  |       |
| INDEX_NAME     | varchar(64)         | YES  |     | <null>  |       |
| TYPE           | varchar(64)         | YES  |     | <null>  |       |
| MAX_HOT_DEGREE | bigint(21) unsigned | YES  |     | <null>  |       |
| REGION_COUNT   | bigint(21) unsigned | YES  |     | <null>  |       |
| FLOW_BYTES     | bigint(21) unsigned | YES  |     | <null>  |       |
+----------------+---------------------+------+-----+---------+-------+
```

## TIDB_INDEXES 表

`TIDB_INDEXES` 记录了所有表中的 INDEX 信息。


```sql
desc TIDB_INDEXES;
```

```
+---------------+---------------------+------+-----+---------+-------+
| Field         | Type                | Null | Key | Default | Extra |
+---------------+---------------------+------+-----+---------+-------+
| TABLE_SCHEMA  | varchar(64)         | YES  |     | <null>  |       |
| TABLE_NAME    | varchar(64)         | YES  |     | <null>  |       |
| NON_UNIQUE    | bigint(21) unsigned | YES  |     | <null>  |       |
| KEY_NAME      | varchar(64)         | YES  |     | <null>  |       |
| SEQ_IN_INDEX  | bigint(21) unsigned | YES  |     | <null>  |       |
| COLUMN_NAME   | varchar(64)         | YES  |     | <null>  |       |
| SUB_PART      | bigint(21) unsigned | YES  |     | <null>  |       |
| INDEX_COMMENT | varchar(2048)       | YES  |     | <null>  |       |
| INDEX_ID      | bigint(21) unsigned | YES  |     | <null>  |       |
+---------------+---------------------+------+-----+---------+-------+
```

`TIDB_INDEXES` 表中列的含义如下：

* `TABLE_SCHEMA`：索引所在表的所属数据库的名称。
* `TABLE_NAME`：索引所在表的名称。
* `NON_UNIQUE`：如果索引是唯一的，则为 `0`，否则为 `1`。
* `KEY_NAME`：索引的名称。如果索引是主键，则名称为 `PRIMARY`。
* `SEQ_IN_INDEX`：索引中列的顺序编号，从 `1` 开始。
* `COLUMN_NAME`：索引所在的列名。
* `SUB_PART`：索引前缀长度。如果列是部分被索引，则该值为被索引的字符数量，否则为 `NULL`。
* `INDEX_COMMENT`：创建索引时以 `COMMENT` 标注的注释。
* `INDEX_ID`：索引的 ID。

## TIKV_REGION_PEERS 表

`TIKV_REGION_PEERS` 表提供了所有 REGION 的 peer 信息。


```sql
desc TIKV_REGION_PEERS;
```

```
+--------------+---------------------+------+-----+---------+-------+
| Field        | Type                | Null | Key | Default | Extra |
+--------------+---------------------+------+-----+---------+-------+
| REGION_ID    | bigint(21) unsigned | YES  |     | <null>  |       |
| PEER_ID      | bigint(21) unsigned | YES  |     | <null>  |       |
| STORE_ID     | bigint(21) unsigned | YES  |     | <null>  |       |
| IS_LEARNER   | tinyint(1) unsigned | YES  |     | <null>  |       |
| IS_LEADER    | tinyint(1) unsigned | YES  |     | <null>  |       |
| STATUS       | varchar(10)         | YES  |     | <null>  |       |
| DOWN_SECONDS | bigint(21) unsigned | YES  |     | <null>  |       |
+--------------+---------------------+------+-----+---------+-------+
```

## TIKV_REGION_STATUS 表

`TIKV_REGION_STATUS` 表提供了所有 REGION 的状态信息。


```sql
desc TIKV_REGION_STATUS;
```

```
+------------------+---------------------+------+-----+---------+-------+
| Field            | Type                | Null | Key | Default | Extra |
+------------------+---------------------+------+-----+---------+-------+
| REGION_ID        | bigint(21) unsigned | YES  |     | <null>  |       |
| START_KEY        | text                | YES  |     | <null>  |       |
| END_KEY          | text                | YES  |     | <null>  |       |
| EPOCH_CONF_VER   | bigint(21) unsigned | YES  |     | <null>  |       |
| EPOCH_VERSION    | bigint(21) unsigned | YES  |     | <null>  |       |
| WRITTEN_BYTES    | bigint(21) unsigned | YES  |     | <null>  |       |
| READ_BYTES       | bigint(21) unsigned | YES  |     | <null>  |       |
| APPROXIMATE_SIZE | bigint(21) unsigned | YES  |     | <null>  |       |
| APPROXIMATE_KEYS | bigint(21) unsigned | YES  |     | <null>  |       |
+------------------+---------------------+------+-----+---------+-------+
```

## TIKV_STORE_STATUS 表

`TIKV_STORE_STATUS` 表提供了所有 TiKV Store 的状态信息。


```sql
desc TIKV_STORE_STATUS;
```

```
+-------------------+---------------------+------+-----+---------+-------+
| Field             | Type                | Null | Key | Default | Extra |
+-------------------+---------------------+------+-----+---------+-------+
| STORE_ID          | bigint(21) unsigned | YES  |     | <null>  |       |
| ADDRESS           | varchar(64)         | YES  |     | <null>  |       |
| STORE_STATE       | bigint(21) unsigned | YES  |     | <null>  |       |
| STORE_STATE_NAME  | varchar(64)         | YES  |     | <null>  |       |
| LABEL             | json unsigned       | YES  |     | <null>  |       |
| VERSION           | varchar(64)         | YES  |     | <null>  |       |
| CAPACITY          | varchar(64)         | YES  |     | <null>  |       |
| AVAILABLE         | varchar(64)         | YES  |     | <null>  |       |
| LEADER_COUNT      | bigint(21) unsigned | YES  |     | <null>  |       |
| LEADER_WEIGHT     | bigint(21) unsigned | YES  |     | <null>  |       |
| LEADER_SCORE      | bigint(21) unsigned | YES  |     | <null>  |       |
| LEADER_SIZE       | bigint(21) unsigned | YES  |     | <null>  |       |
| REGION_COUNT      | bigint(21) unsigned | YES  |     | <null>  |       |
| REGION_WEIGHT     | bigint(21) unsigned | YES  |     | <null>  |       |
| REGION_SCORE      | bigint(21) unsigned | YES  |     | <null>  |       |
| REGION_SIZE       | bigint(21) unsigned | YES  |     | <null>  |       |
| START_TS          | datetime unsigned   | YES  |     | <null>  |       |
| LAST_HEARTBEAT_TS | datetime unsigned   | YES  |     | <null>  |       |
| UPTIME            | varchar(64)         | YES  |     | <null>  |       |
+-------------------+---------------------+------+-----+---------+-------+
```

## USER_PRIVILEGES 表

`USER_PRIVILEGES` 表提供了关于全局权限的信息。该表的数据根据 `mysql.user` 系统表生成。


```sql
desc USER_PRIVILEGES;
```

```
+----------------|--------------|------|------|---------|-------+
| Field          | Type         | Null | Key  | Default | Extra |
+----------------|--------------|------|------|---------|-------+
| GRANTEE        | varchar(81)  | YES  |      | NULL    |       |
| TABLE_CATALOG  | varchar(512) | YES  |      | NULL    |       |
| PRIVILEGE_TYPE | varchar(64)  | YES  |      | NULL    |       |
| IS_GRANTABLE   | varchar(3)   | YES  |      | NULL    |       |
+----------------|--------------|------|------|---------|-------+
4 rows in set (0.00 sec)
```

`USER_PRIVILEGES` 表中列的含义如下：

* `GRANTEE`：被授权的用户名称，格式为 `'user_name'@'host_name'`。
* `TABLE_CATALOG`：表所属的目录的名称。该值始终为 `def`。
* `PRIVILEGE_TYPE`：被授权的权限类型，每行只列一个权限。
* `IS_GRANTABLE`：如果用户有 `GRANT OPTION` 的权限，则为 `YES`，否则为 `NO`。

## VIEWS 表

`VIEWS` 表提供了关于 SQL 视图的信息。


```sql
create view test.v1 as select 1;
```

```
Query OK, 0 rows affected (0.00 sec)
```


```sql
select * from views;
```

```
*************************** 1. row ***************************
       TABLE_CATALOG: def
        TABLE_SCHEMA: test
          TABLE_NAME: v1
     VIEW_DEFINITION: select 1
        CHECK_OPTION: CASCADED
        IS_UPDATABLE: NO
             DEFINER: root@127.0.0.1
       SECURITY_TYPE: DEFINER
CHARACTER_SET_CLIENT: utf8
COLLATION_CONNECTION: utf8_general_ci
1 row in set (0.00 sec)
```

`VIEWS` 表中列的含义如下：

* `TABLE_CATALOG`：视图所属的目录的名称。该值始终为 `def`。
* `TABLE_SCHEMA`：视图所属的数据库的名称。
* `TABLE_NAME`：视图名称。
* `VIEW_DEFINITION`：视图的定义，由创建视图时 `SELECT` 部分的语句组成。
* `CHECK_OPTION`：`CHECK_OPTION` 的值。取值为 `NONE`、 `CASCADE` 或 `LOCAL`。
* `IS_UPDATABLE`：`UPDATE`/`INSERT`/`DELETE` 是否对该视图可用。在 TiDB，始终为 `NO`。 
* `DEFINER`：视图的创建者用户名称，格式为 `'user_name'@'host_name'`。
* `SECURITY_TYPE`：`SQL SECURITY` 的值，取值为 `DEFINER` 或 `INVOKER`。
* `CHARACTER_SET_CLIENT`：在视图创建时 session 变量 `character_set_client` 的值。
* `COLLATION_CONNECTION`：在视图创建时 session 变量 `collation_connection` 的值。

## 不支持的 Information Schema 表

TiDB 包含以下 `INFORMATION_SCHEMA` 表，但仅会返回空行：

* `COLUMN_PRIVILEGES`
* `EVENTS`
* `FILES`
* `GLOBAL_STATUS`
* `GLOBAL_VARIABLES`
* `OPTIMIZER_TRACE`
* `PARAMETERS`
* `PARTITIONS`
* `PLUGINS`
* `PROFILING`
* `REFERENTIAL_CONSTRAINTS`
* `ROUTINES`
* `SCHEMA_PRIVILEGES`
* `SESSION_STATUS`
* `TABLESPACES`
* `TABLE_PRIVILEGES`
* `TRIGGERS`
