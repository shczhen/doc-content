---
title: TiDB 专用系统变量和语法
aliases: ['/docs-cn/v2.1/tidb-specific-system-variables/','/docs-cn/v2.1/reference/configuration/tidb-server/tidb-specific-variables/']
---

# TiDB 专用系统变量和语法

TiDB 在 MySQL 的基础上，定义了一些专用的系统变量和语法用来优化性能。

## 系统变量

变量可以通过 SET 语句设置，例如

```
set @@tidb_distsql_scan_concurrency = 10
```

如果需要设值全局变量，执行

```
set @@global.tidb_distsql_scan_concurrency = 10
```

### tidb_snapshot

作用域：SESSION

默认值：空字符串

这个变量用来设置当前会话期待读取的历史数据所处时刻。比如当设置为 "2017-11-11 20:20:20" 时或者一个 TSO 数字 "400036290571534337"，当前会话将能读取到该时刻的数据。

### tidb_import_data

作用域：SESSION

默认值：0

这个变量用来表示当前状态是否为从 dump 文件中导入数据。
当这个变量被设置为 1 时，唯一索引约束不被检查以加速导入速度。
这个变量不对外用，只是给 lightning 使用，请用户不要自行修改。

### tidb_opt_agg_push_down

作用域：SESSION

默认值：0

这个变量用来设置优化器是否执行聚合函数下推到 Join 之前的优化操作。
当查询中聚合操作执行很慢时，可以尝试设置该变量为 1。

### tidb_opt_insubquery_unfold

作用域：SESSION | GLOBAL

默认值：0

这个变量用来设置优化器是否执行 `in-` 子查询展开的优化操作。

### tidb_build_stats_concurrency

作用域：SESSION

默认值：4

这个变量用来设置 ANALYZE 语句执行时并发度。
当这个变量被设置得更大时，会对其它的查询语句执行性能产生一定影响。

### tidb_checksum_table_concurrency

作用域：SESSION

默认值：4

这个变量用来设置 ADMIN CHECKSUM TABLE 语句执行时扫描索引的并发度。
当这个变量被设置得更大时，会对其它的查询语句执行性能产生一定影响。

### tidb_current_ts

作用域：SESSION

默认值：0

这个变量是一个只读变量，用来获取当前事务的时间戳。

### tidb_config

作用域：SESSION

默认值：空字符串

这个变量是一个只读变量，用来获取当前 TiDB Server 的配置信息。

### tidb_distsql_scan_concurrency

作用域：SESSION | GLOBAL

默认值：15

这个变量用来设置 scan 操作的并发度，AP 类应用适合较大的值，TP 类应用适合较小的值。
对于 AP 类应用，最大值建议不要超过所有 TiKV 节点的 CPU 核数。

### tidb_index_lookup_size

作用域：SESSION | GLOBAL

默认值：20000

这个变量用来设置 index lookup 操作的 batch 大小，AP 类应用适合较大的值，TP 类应用适合较小的值。

### tidb_index_lookup_concurrency

作用域：SESSION | GLOBAL

默认值：4

这个变量用来设置 index lookup 操作的并发度，AP 类应用适合较大的值，TP 类应用适合较小的值。

### tidb_index_lookup_join_concurrency

作用域：SESSION | GLOBAL

默认值：4

这个变量用来设置 index lookup join 算法的并发度。

### tidb_hash_join_concurrency

作用域：SESSION | GLOBAL

默认值：5

这个变量用来设置 hash join 算法的并发度。

### tidb_index_serial_scan_concurrency

作用域：SESSION | GLOBAL

默认值：1

这个变量用来设置顺序 scan 操作的并发度，AP 类应用适合较大的值，TP 类应用适合较小的值。

### tidb_projection_concurrency

作用域：SESSION | GLOBAL

默认值：4

这个变量用来设置 Projection 算子的并发度。

### tidb_hashagg_partial_concurrency

作用域：SESSION | GLOBAL

默认值：4

这个变量用来设置并行 hash aggregation 算法 partial 阶段的执行并发度。对于聚合函数参数不为 distinct 的情况，HashAgg 分为 partial 和 final 阶段分别并行执行。

### tidb_hashagg_final_concurrency

作用域：SESSION | GLOBAL

默认值：4

这个变量用来设置并行 hash aggregation 算法 final 阶段的执行并发度。对于聚合函数参数不为 distinct 的情况，HashAgg 分为 partial 和 final 阶段分别并行执行。

### tidb_index_join_batch_size

作用域：SESSION | GLOBAL

默认值：25000

这个变量用来设置 index lookup join 操作的 batch 大小，AP 类应用适合较大的值，TP 类应用适合较小的值。

### tidb_skip_utf8_check

作用域：SESSION | GLOBAL

默认值：0

这个变量用来设置是否跳过 UTF-8 字符的验证。
验证 UTF-8 字符需要消耗一定的性能，当可以确认输入的字符串为有效的 UTF-8 字符时，可以将其设置为 1。

### tidb_max_chunk_size

作用域：SESSION | GLOBAL

默认值：1024

这个变量用来设置执行过程中一个 chunk 最大的行数。

### tidb_mem_quota_query

作用域：SESSION

默认值：32 GB

这个变量用来设置一条查询语句的内存使用阈值。
如果一条查询语句执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_hashjoin

作用域：SESSION

默认值：32 GB

这个变量用来设置 HashJoin 算子的内存使用阈值。
如果 HashJoin 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_mergejoin

作用域：SESSION

默认值：32 GB

这个变量用来设置 MergeJoin 算子的内存使用阈值。
如果 MergeJoin 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_sort

作用域：SESSION

默认值：32 GB

这个变量用来设置 Sort 算子的内存使用阈值。
如果 Sort 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_topn

作用域：SESSION

默认值：32 GB

这个变量用来设置 TopN 算子的内存使用阈值。
如果 TopN 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_indexlookupreader

作用域：SESSION

默认值：32 GB

这个变量用来设置 IndexLookupReader 算子的内存使用阈值。

如果 IndexLookupReader 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_indexlookupjoin

作用域：SESSION

默认值：32 GB

这个变量用来设置 IndexLookupJoin 算子的内存使用阈值。
如果 IndexLookupJoin 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_mem_quota_nestedloopapply

作用域：SESSION

默认值：32 GB

这个变量用来设置 NestedLoopApply 算子的内存使用阈值。
如果 NestedLoopApply 算子执行过程中使用的内存空间超过该阈值，会触发 TiDB 启动配置文件中 OOMAction 项所指定的行为。

### tidb_general_log

作用域：SERVER

默认值：0

这个变量用来设置是否在日志里记录所有的 SQL 语句。

### tidb_enable_streaming

作用域：SESSION

默认值：0

这个变量用来设置是否启用 Streaming。

### tidb_retry_limit

作用域：SESSION | GLOBAL

默认值：10

这个变量用来设置最大重试次数。一个事务执行中遇到可重试的错误（例如事务冲突、事务提交过慢或表结构变更）时，会根据该变量的设置进行重试。注意当 `tidb_retry_limit = 0` 时，也会禁用自动重试。

### tidb_disable_txn_auto_retry

作用域：SESSION | GLOBAL

默认值：off

这个变量用来设置是否禁用显式事务自动重试，设置为 `on` 时，不会自动重试，如果遇到事务冲突需要在应用层重试。TiDB v2.1 默认开启自动重试。

如果将该变量的值设为 `off`，TiDB 将会自动重试事务，这样在事务提交时遇到的错误更少。需要注意的是，这样可能会导致数据更新丢失。

这个变量不会影响自动提交的隐式事务和 TiDB 内部执行的事务，它们依旧会根据 `tidb_retry_limit` 的值来决定最大重试次数。

是否需要禁用自动重试，请参考[重试的局限性](/optimistic-transaction.md#重试的局限性)。

### tidb_backoff_weight

作用域：SESSION | GLOBAL

默认值：2

这个变量用来给 TiDB 的 `backoff` 最大时间增加权重，即内部遇到网络或其他组件（TiKV、PD）故障等时，发送重试请求的最大重试时间。可以通过这个变量来调整最大重试时间，最小值为 1。

例如，TiDB 向 PD 取 TSO 的基础超时时间是 15 秒，当 `tidb_backoff_weight = 2` 时，取 TSO 的最大超时时间为：基础时间 \* 2 等于 30 秒。

在网络环境较差的情况下，适当增大该变量值可以有效缓解因为超时而向应用端报错的情况；而如果应用端希望更快地接到报错信息，则应该尽量减小该变量的值。

### tidb_enable_table_partition

作用域：SESSION

默认值：0

这个变量用来设置是否开启 TABLE PARTITION 特性。

### tidb_backoff_lock_fast

作用域：SESSION | GLOBAL

默认值：100

这个变量用来设置读请求遇到锁的 backoff 时间。

### tidb_ddl_reorg_worker_cnt

作用域：GLOBAL

默认值（2.1.17 之前版本）：16
默认值（2.1.17 及之后版本）：4

这个变量用来设置 DDL 操作 re-organize 阶段的并发度。

### tidb_ddl_reorg_batch_size

作用域：GLOBAL

默认值（2.1.17 以前版本）：1024
默认值（2.1.17 及以后版本）：256

这个变量用来设置 DDL 操作 re-organize 阶段的 batch size。比如 Add Index 操作，需要回填索引数据，通过并发 tidb_ddl_reorg_worker_cnt 个 worker 一起回填数据，每个 worker 以 batch 为单位进行回填。如果 Add Index 时有较多 Update 操作或者 Replace 等更新操作，batch size 越大，事务冲突的概率也会越大，此时建议调小 batch size 的值，最小值是 32。在没有事务冲突的情况下，batch size 可设为较大值，最大值是 10240，这样回填数据的速度更快，但是 TiKV 的写入压力也会变大。

### tidb_ddl_reorg_priority

作用域：SESSION

默认值：PRIORITY_LOW

这个变量用来设置 `ADD INDEX` 操作 re-organize 阶段的执行优先级，可设置为 PRIORITY_LOW/PRIORITY_NORMAL/PRIORITY_HIGH。

### tidb_max_delta_schema_count

作用域：GLOBAL

默认值：1024

这个变量用来设置缓存 schema 版本信息（对应版本修改的相关 table IDs）的个数限制，可设置的范围 100 - 16384。此变量在 2.1.18 及之后版本支持。

### tidb_force_priority

作用域：SESSION

默认值：`NO_PRIORITY`

这个变量用于改变 TiDB server 上执行的语句的默认优先级。例如，你可以通过设置该变量来确保正在执行 OLAP 查询的用户优先级低于正在执行 OLTP 查询的用户。

可设置为 `NO_PRIORITY`、`LOW_PRIORITY`、`DELAYED` 或 `HIGH_PRIORITY`。

### SHARD_ROW_ID_BITS

对于 PK 非整数或没有 PK 的表，TiDB 会使用一个隐式的自增 rowid，大量 `INSERT` 时会把数据集中写入单个 Region，造成写入热点。

通过设置 `SHARD_ROW_ID_BITS`，可以把 rowid 打散写入多个不同的 Region，缓解写入热点问题。但是设置的过大会造成 RPC 请求数放大，增加 CPU 和网络开销。

- `SHARD_ROW_ID_BITS = 4` 表示 16 个分片
- `SHARD_ROW_ID_BITS = 6` 表示 64 个分片
- `SHARD_ROW_ID_BITS = 0` 表示默认值 1 个分片

语句示例：

- `CREATE TABLE`：`CREATE TABLE t (c int) SHARD_ROW_ID_BITS = 4;`
- `ALTER TABLE`：`ALTER TABLE t SHARD_ROW_ID_BITS = 4;`

### tidb_slow_log_threshold

作用域：SESSION

默认值：300

输出慢日志的耗时阈值。当查询大于这个值，就会当做是一个慢查询，输出到慢查询日志。默认为 300ms。

示例：

```sql
set tidb_slow_log_threshold = 200
```

### tidb_query_log_max_len

作用域：SESSION

默认值：2048 (bytes)

最长的 SQL 输出长度。当语句的长度大于 query-log-max-len，将会被截断输出。

示例：

```sql
set tidb_query_log_max_len = 20
```

### tidb_scatter_region

作用域：GLOBAL

默认值：0

TiDB 默认会在建表时为新表分裂 Region。开启该变量后，会在建表语句执行时，同步打散刚分裂出的 Region。适用于批量建表后紧接着批量写入数据，能让刚分裂出的 Region 先在 TiKV 分散而不用等待 PD 进行调度。为了保证后续批量写入数据的稳定性，建表语句会等待打散 Region 完成后再返回建表成功，建表语句执行时间会是关闭该变量的数倍。

### tidb_allow_remove_auto_inc <span class="version-mark">从 v2.1.18 版本开始引入</span>

作用域：SESSION

默认值：0

这个变量用来控制是否允许通过 `ALTER TABLE MODIFY` 或 `ALTER TABLE CHANGE` 来移除某个列的 `AUTO_INCREMENT` 属性。默认为不允许。
