---
title: DM 1.0-GA 性能测试报告
aliases: ['/docs-cn/tidb-data-migration/stable/benchmark-v1.0-ga/','/docs-cn/tidb-data-migration/v1.0/benchmark-v1.0-ga/','/docs-cn/dev/benchmark/dm-v1.0-ga/','/docs-cn/v3.0/benchmark/dm-v1.0-ga/','/docs-cn/v3.1/benchmark/dm-v1.0-ga/','/docs-cn/stable/benchmark/dm-v1.0-ga/']
---

# DM 1.0-GA 性能测试报告

本报告记录了对 1.0-GA 版本的 DM 进行性能测试的目的、环境、场景和结果。

## 测试目的

该性能测试用于评估使用 DM 进行全量数据导入和增量数据复制的性能上限，并根据测试结果提供 DM 迁移任务的参考配置。

## 测试环境

### 测试机器信息

系统信息：

| 机器 IP     | 操作系统                      | 内核版本                  | 文件系统类型     |
| :---------: | :---------------------------: | :-----------------------: | :--------------: |
| 172.16.4.39 | CentOS Linux release 7.6.1810 | 3.10.0-957.1.3.el7.x86_64 | ext4             |
| 172.16.4.40 | CentOS Linux release 7.6.1810 | 3.10.0-957.1.3.el7.x86_64 | ext4             |
| 172.16.4.41 | CentOS Linux release 7.6.1810 | 3.10.0-957.1.3.el7.x86_64 | ext4             |
| 172.16.4.42 | CentOS Linux release 7.6.1810 | 3.10.0-957.1.3.el7.x86_64 | ext4             |
| 172.16.4.43 | CentOS Linux release 7.6.1810 | 3.10.0-957.1.3.el7.x86_64 | ext4             |
| 172.16.4.44 | CentOS Linux release 7.6.1810 | 3.10.0-957.1.3.el7.x86_64 | ext4             |

硬件信息：

| 类别         | 指标                                                |
| :----------: | :-------------------------------------------------: |
| CPU          | 40 vCPUs, Intel(R) Xeon(R) CPU E5-2630 v4 @ 2.20GHz |
| 内存         | 192GB, 12 条 16GB DIMM DDR4 2133 MHz                |
| 磁盘         | Intel DC P4510 4TB NVMe PCIe 3.0                    |
| 网卡         | 万兆网卡                                            |

其他：

* 服务器间网络延迟：rtt min/avg/max/mdev = 0.074/0.088/0.121/0.019 ms

### 集群拓扑

| 机器 IP     | 部署的服务                         |
| :---------: | :--------------------------------: |
| 172.16.4.39 | PD1, DM-worker1, DM-master         |
| 172.16.4.40 | PD2, MySQL1                        |
| 172.16.4.41 | PD3, TiDB                          |
| 172.16.4.42 | TiKV1                              |
| 172.16.4.43 | TiKV2                              |
| 172.16.4.44 | TiKV3                              |

### 各服务版本信息

- MySQL 版本: 5.7.27-log
- TiDB 版本: v4.0.0-alpha-198-gbde7f440e
- DM 版本: v1.0.1
- Sysbench 版本: 1.0.17

## 测试场景

### 迁移数据流

MySQL1 (172.16.4.40) -> DM-worker1 (172.16.4.39) -> TiDB (172.16.4.41)

### 公共配置信息

#### 迁移数据表结构


```sql
CREATE TABLE `sbtest` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `k` int(11) NOT NULL DEFAULT '0',
  `c` char(120) CHARSET utf8mb4 COLLATE utf8mb4_bin NOT NULL DEFAULT '',
  `pad` char(60) CHARSET utf8mb4 COLLATE utf8mb4_bin NOT NULL DEFAULT '',
  PRIMARY KEY (`id`),
  KEY `k_1` (`k`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
```

#### 数据库配置

使用 TiDB Ansible 部署 TiDB 测试集群，所有配置使用 TiDB Ansible 提供的默认配置。

### 全量导入性能测试用例

#### 测试过程

- 部署测试环境
- 使用 `sysbench` 在上游创建测试表，并生成全量导入的测试数据
- 在 `full` 模式下启动 DM 迁移任务

`sysbench` 生成数据的命令如下所示：


```bash
sysbench --test=oltp_insert --tables=4 --mysql-host=172.16.4.40 --mysql-port=3306 --mysql-user=root --mysql-db=dm_benchmark --db-driver=mysql --table-size=50000000 prepare
```

#### 全量导入性能测试结果

在 `mydumpers` 中使用 `--rows` 选项可以开启单表多线程并发导出（当前 `mydumpers` 在 MySQL 的单表并发会优先选出一列做拆分基准，选择优先级为主键>唯一索引>普通索引，选出目标列后需保证该列为 INT 类型；针对 `TiDB` 的并发导出则会优先尝试 `_tidb_rowid` 列），以下的第一项测试用于验证开启单表并发导出可以提高数据导出性能。

| 测试项             | 导出线程数  | mydumpers extra-args 参数            | 导出速度 (MB/s)   |
| :----------------: | :---------: | :---------------------------------: | :---------------: |
| 开启单表并发导出   | 32          | "--rows 320000 --regex '^sbtest.*'" | 191.03            |
| 不开启单表并发导出 |  4          | "--regex '^sbtest.*'"               | 72.22             |

| 测试项    | 事务执行延迟 (s) | 每条插入语句包含的行数 | 导入数据量 (GB) | 导入时间 (s) | 导入速度 (MB/s) |
| :-------: | :--------------: | :--------------------: | :-------------: | :----------: | :-------------: |
| load data | 1.737            | 4878                   | 38.14           | 2346.9       | 16.64           |

#### 在 load 处理单元使用不同 pool size 的性能测试对比

该测试中全量导入的数据量为 3.78 GB，使用以下 `sysbench` 命令生成：


```bash
sysbench --test=oltp_insert --tables=4 --mysql-host=172.16.4.40 --mysql-port=3306 --mysql-user=root --mysql-db=dm_benchmark --db-driver=mysql --table-size=5000000 prepare
```

| load 处理单元 pool size | 事务执行时间 (s) | 导入时间 (s) | 导入速度 (MB/s) | TiDB 99 duration (s) |
| :---------------------: | :--------------: | :----------: | :-------------: | :------------------: |
| 2                       | 0.250            | 425.9        | 9.1             | 0.23                 |
| 4                       | 0.523            | 360.1        | 10.7            | 0.41                 |
| 8                       | 0.986            | 267.0        | 14.5            | 0.93                 |
| 16                      | 2.022            | 265.9        | 14.5            | 2.68                 |
| 32                      | 3.778            | 262.3        | 14.7            | 6.39                 |
| 64                      | 7.452            | 281.9        | 13.7            | 8.00                 |

#### 导入数据时每条插入语句包含行数不同的情况下的性能测试对比

该测试中全量导入的数据量为 3.78 GB，load 处理单元 `pool-size` 大小为 32。插入语句包含行数通过 dump 处理单元的 `--statement-size` 来控制。

| 每条语句中包含的行数       | mydumpers extra-args 参数  | 事务执行时间 (s)  | 导入时间 (s) | 导入速度 (MB/s) | TiDB 99 duration (s) |
| :------------------------: | :-----------------------: | :---------------: | :----------: | :-------------: | :------------------: |
|            7426            | -s 1500000 -r 320000      |   6.982           |  258.3       |     15.0        |        10.34         |
|            4903            | -s 1000000 -r 320000      |   3.778           |  262.3       |     14.7        |         6.39         |
|            2470            | -s 500000 -r 320000       |   1.962           |  271.36      |     14.3        |         2.00         |
|            1236            | -s 250000 -r 320000       |   1.911           |  283.3       |     13.7        |         1.50         |
|            618             | -s 125000 -r 320000       |   0.683           |  299.9       |     12.9        |         0.73         |
|            310             |  -s 62500 -r 320000       |   0.413           |  322.6       |     12.0        |         0.49         |

### 增量复制性能测试用例

#### 测试过程

- 部署测试环境
- 使用 `sysbench` 在上游创建测试表，并生成全量导入的测试数据
- 在 `all` 模式下启动 DM 迁移任务，等待迁移任务进入 `sync` 迁移阶段
- 使用 `sysbench` 在上游持续生成增量数据，通过 `query-status` 命令观测 DM 的复制状态，通过 Grafana 观测 DM 和 TiDB 的监控指标。

#### 增量复制性能测试结果

上游 `sysbench` 生成增量数据命令


```bash
sysbench --test=oltp_insert --tables=4 --num-threads=32 --mysql-host=172.17.4.40 --mysql-port=3306 --mysql-user=root --mysql-db=dm_benchmark --db-driver=mysql --report-interval=10 --time=1800 run
```

该性能测试中迁移任务 `sync` 处理单元 `worker-count` 设置为 32，`batch` 大小设置为 100。

| 组件                       | qps                                                          | tps                                                             | 95% 延迟                     |
| :------------------------: | :----------------------------------------------------------: | :-------------------------------------------------------------: | :--------------------------: |
| MySQL                      | 42.79k                                                       | 42.79k                                                          | 1.18ms                       |
| DM relay log unit          | -                                                            | 11.3MB/s                                                        | 45us (binlog 读延迟)         |
| DM binlog replication unit | 22.97k (binlog event 接收qps，不包括忽略的 event)            | -                                                               | 20ms (事务执行时间)          |
| TiDB                       | 31.30k (Begin/Commit 3.93k Insert 22.76k)                    | 4.16k                                                           | 95%: 6.4ms 99%: 9ms          |

#### 在 sync 处理单元使用不同并发度的性能测试对比

| sync 处理单元 worker-count 数 | DM 内部 job tps | DM 事务执行时间 (ms)      | TiDB qps | TiDB 99 duration (ms) |
| :---------------------------: | :-------------: | :-----------------------: | :------: | :-------------------: |
| 4                             | 7074            | 63                        | 7.1k     | 3                     |
| 8                             | 14684           | 64                        | 14.9k    | 4                     |
| 16                            | 23486           | 56                        | 24.9k    | 6                     |
| 32                            | 23345           | 28                        | 29.2k    | 10                    |
| 64                            | 23302           | 30                        | 31.2k    | 16                    |
| 1024                          | 22225           | 70                        | 56.9k    | 70                    |

#### 不同数据分布的增量复制性能测试对比

| sysbench 语句类型| DM relay log 持久化速率 (MB/s) | DM 内部 job tps | DM 事务执行时间 (ms) | TiDB qps | TiDB 99 duration (ms) |
| :--------------: | :----------------------------: | :-------------: | :------------------: | :------: | :-------------------: |
| insert_only      | 11.3                           | 23345           | 28                   | 29.2k    | 10                    |
| write_only       | 18.7                           | 33470           | 129                  | 34.6k    | 11                    |

## 推荐迁移任务参数配置

### dump 处理单元

推荐每一条插入语句的大小在 200KB ~ 1MB 之间，相应每条语句包含的行数大约在 1000-5000（具体包含的语句行数与实际场景中每行数据大小有关）。

### load 处理单元

推荐 `pool-size` 设置为 16。

### sync 处理单元

推荐将 `batch` 设置为 100，`worker-count` 设置为 16 ~ 32。
