---
title: SHOW [GLOBAL|SESSION] VARIABLES
summary: TiDB 数据库中 SHOW [GLOBAL|SESSION] VARIABLES 的使用概况。
aliases: ['/docs-cn/v3.1/sql-statements/sql-statement-show-variables/','/docs-cn/v3.1/reference/sql/statements/show-variables/']
---

# SHOW [GLOBAL|SESSION] VARIABLES

`SHOW [GLOBAL|SESSION] VARIABLES` 语句用于显示 `GLOBAL` 或 `SESSION` 范围的变量列表。如果未指定范围，则应用默认范围 `SESSION`。

## 语法图

**ShowStmt:**

![ShowStmt](https://download.pingcap.com/images/docs-cn/sqlgram/ShowStmt.png)

**ShowTargetFilterable:**

![ShowTargetFilterable](https://download.pingcap.com/images/docs-cn/sqlgram/ShowTargetFilterable.png)

**GlobalScope:**

![GlobalScope](https://download.pingcap.com/images/docs-cn/sqlgram/GlobalScope.png)

## 示例


```sql
SHOW GLOBAL VARIABLES LIKE 'tidb%';
```

```
+-------------------------------------+---------------+
| Variable_name                       | Value         |
+-------------------------------------+---------------+
| tidb_retry_limit                    | 10            |
| tidb_enable_streaming               | 0             |
| tidb_hashagg_final_concurrency      | 4             |
| tidb_disable_txn_auto_retry         | 1             |
| tidb_mem_quota_query                | 34359738368   |
| tidb_skip_isolation_level_check     | 0             |
| tidb_ddl_reorg_batch_size           | 1024          |
| tidb_constraint_check_in_place      | 0             |
| tidb_current_ts                     | 0             |
| tidb_opt_insubq_to_join_and_agg     | 1             |
| tidb_enable_window_function         | 1             |
| tidb_ddl_error_count_limit          | 512           |
| tidb_checksum_table_concurrency     | 4             |
| tidb_config                         |               |
| tidb_batch_insert                   | 0             |
| tidb_opt_join_reorder_threshold     | 0             |
| tidb_auto_analyze_end_time          | 23:59 +0000   |
| tidb_index_lookup_size              | 20000         |
| tidb_projection_concurrency         | 4             |
| tidb_opt_correlation_threshold      | 0.9           |
| tidb_enable_table_partition         | auto          |
| tidb_wait_split_region_timeout      | 300           |
| tidb_skip_utf8_check                | 0             |
| tidb_dml_batch_size                 | 20000         |
| tidb_backoff_weight                 | 2             |
| tidb_txn_mode                       |               |
| tidb_mem_quota_indexlookupjoin      | 34359738368   |
| tidb_hashagg_partial_concurrency    | 4             |
| tidb_enable_radix_join              | 0             |
| tidb_mem_quota_topn                 | 34359738368   |
| tidb_hash_join_concurrency          | 5             |
| tidb_max_chunk_size                 | 1024          |
| tidb_mem_quota_indexlookupreader    | 34359738368   |
| tidb_expensive_query_time_threshold | 60            |
| tidb_enable_cascades_planner        | 0             |
| tidb_mem_quota_mergejoin            | 34359738368   |
| tidb_auto_analyze_ratio             | 0.5           |
| tidb_index_lookup_concurrency       | 4             |
| tidb_force_priority                 | NO_PRIORITY   |
| tidb_ddl_reorg_priority             | PRIORITY_LOW  |
| tidb_index_lookup_join_concurrency  | 4             |
| tidb_index_join_batch_size          | 25000         |
| tidb_index_serial_scan_concurrency  | 1             |
| tidb_mem_quota_nestedloopapply      | 34359738368   |
| tidb_auto_analyze_start_time        | 00:00 +0000   |
| tidb_snapshot                       |               |
| tidb_low_resolution_tso             | 0             |
| tidb_batch_commit                   | 0             |
| tidb_init_chunk_size                | 32            |
| tidb_build_stats_concurrency        | 4             |
| tidb_backoff_lock_fast              | 100           |
| tidb_optimizer_selectivity_level    | 0             |
| tidb_batch_delete                   | 0             |
| tidb_distsql_scan_concurrency       | 15            |
| tidb_scatter_region                 | 0             |
| tidb_opt_correlation_exp_factor     | 1             |
| tidb_ddl_reorg_worker_cnt           | 16            |
| tidb_wait_split_region_finish       | 1             |
| tidb_mem_quota_sort                 | 34359738368   |
| tidb_general_log                    | 0             |
| tidb_opt_write_row_id               | 0             |
| tidb_check_mb4_value_in_utf8        | 1             |
| tidb_enable_fast_analyze            | 0             |
| tidb_slow_query_file                | tidb-slow.log |
| tidb_slow_log_threshold             | 300           |
| tidb_opt_agg_push_down              | 0             |
| tidb_mem_quota_hashjoin             | 34359738368   |
| tidb_query_log_max_len              | 2048          |
+-------------------------------------+---------------+
68 rows in set (0.01 sec)
```


```sql
SHOW GLOBAL VARIABLES LIKE 'time_zone%';
```

```
+---------------+--------+
| Variable_name | Value  |
+---------------+--------+
| time_zone     | SYSTEM |
+---------------+--------+
1 row in set (0.00 sec)
```

## MySQL 兼容性

`SHOW [GLOBAL|SESSION] VARIABLES` 语句与 MySQL 完全兼容。如发现任何兼容性差异，请在 GitHub 上提交 [issue](https://github.com/pingcap/tidb/issues/new/choose)。

## 另请参阅

* [`SET [GLOBAL|SESSION]`](/sql-statements/sql-statement-set-variable.md)
