---
title: KILL [TIDB]
summary: TiDB 数据库中 KILL [TIDB] 的使用概况。
---

# KILL [TIDB]

`KILL TIDB` 语句用于终止 TiDB 中的连接。

## 语法图

```ebnf+diagram
KillStmt ::= KillOrKillTiDB ( 'CONNECTION' | 'QUERY' )? NUM

KillOrKillTiDB ::= 'KILL' 'TIDB'?
```

## 示例


```sql
SHOW PROCESSLIST;
```

```
+------+------+-----------+------+---------+------+-------+------------------+
| Id   | User | Host      | db   | Command | Time | State | Info             |
+------+------+-----------+------+---------+------+-------+------------------+
|    1 | root | 127.0.0.1 | test | Query   |    0 | 2     | SHOW PROCESSLIST |
|    2 | root | 127.0.0.1 |      | Sleep   |    4 | 2     |                  |
+------+------+-----------+------+---------+------+-------+------------------+
2 rows in set (0.00 sec)
```


```sql
KILL TIDB 2;
```

```
Query OK, 0 rows affected (0.00 sec)
```

## MySQL 兼容性

* 按照设计，`KILL TIDB` 语句默认与 MySQL 不兼容。负载均衡器后面通常放有多个 TiDB 服务器，这种默认不兼容有助于防止在错误的 TiDB 服务器上终止连接。
* `KILL TIDB` 语句是 TiDB 的扩展语法。如果正尝试终止的会话位于同一个 TiDB 服务器上，可在配置文件里设置 [`compatible-kill-query = true`](/tidb-configuration-file.md#compatible-kill-query)。

## 另请参阅

* [SHOW \[FULL\] PROCESSLIST](/sql-statements/sql-statement-show-processlist.md)
* [CLUSTER_PROCESSLIST](/information-schema/information-schema-processlist.md#cluster_processlist)
