---
title: LOAD DATA
summary: TiDB 数据库中 LOAD DATA 的使用概况。
aliases: ['/docs-cn/v2.1/sql-statements/sql-statement-load-data/','/docs-cn/v2.1/reference/sql/statements/load-data/']
---

# LOAD DATA

`LOAD DATA` 语句用于将数据批量加载到 TiDB 表中。

## 语法图

**LoadDataStmt:**

![LoadDataStmt](https://download.pingcap.com/images/docs-cn/sqlgram/LoadDataStmt.png)

## 示例

```sql
mysql> CREATE TABLE trips (
    ->  trip_id bigint NOT NULL PRIMARY KEY AUTO_INCREMENT,
    ->  duration integer not null,
    ->  start_date datetime,
    ->  end_date datetime,
    ->  start_station_number integer,
    ->  start_station varchar(255),
    ->  end_station_number integer,
    ->  end_station varchar(255),
    ->  bike_number varchar(255),
    ->  member_type varchar(255)
    -> );
Query OK, 0 rows affected (0.14 sec)

mysql> LOAD DATA LOCAL INFILE '/mnt/evo970/data-sets/bikeshare-data/2017Q4-capitalbikeshare-tripdata.csv' INTO TABLE trips FIELDS TERMINATED BY ',' ENCLOSED BY '\"' LINES TERMINATED BY '\r\n' IGNORE 1 LINES (duration, start_date, end_date, start_station_number, start_station, end_station_number, end_station, bike_number, member_type);

Query OK, 815264 rows affected (39.63 sec)
Records: 815264  Deleted: 0  Skipped: 0  Warnings: 0
```

## MySQL 兼容性

* 默认情况下，TiDB 每 20,000 行会进行一次提交。这类似于 MySQL NDB Cluster，但并非 InnoDB 存储引擎的默认配置。

> **注意：**
>
> 这种拆分事务提交的方式是以打破事务的原子性和隔离性为代价的，使用该特性时，使用者需要保证没有其他对正在处理的表的**任何**操作，并且在出现报错时，需要及时**人工介入，检查数据的一致性和完整性**。因此，不建议对读写频繁的表使用 `LOAD DATA` 语句。

## 另请参阅

* [INSERT](/sql-statements/sql-statement-insert.md)
