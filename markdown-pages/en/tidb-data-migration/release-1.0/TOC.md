<!-- markdownlint-disable MD007 -->
<!-- markdownlint-disable MD041 -->

- Overview
  - [DM Overview](overview.md)
  - [Restrictions](overview.md#usage-restrictions)
  - [DM-worker](dm-worker-intro.md)
  - [DM Relay Log](relay-log.md)
- Features
  - [Table Routing](feature-overview.md#table-routing)
  - [Block and Allow Lists](feature-overview.md#block-and-allow-table-lists)
  - [Binlog Event Filter](feature-overview.md#binlog-event-filter)
  - [Migration Delay Monitoring](feature-overview.md#migration-delay-monitoring)
  - [Online-ddl-scheme](feature-online-ddl-scheme.md)
  - Sharding Support
    - [Introduction](feature-shard-merge.md)
    - [Restrictions](feature-shard-merge.md#restrictions)
    - [Handle Sharding DDL Locks Manually](feature-manually-handling-sharding-ddl-locks.md)
- Benchmark
  - [DM 1.0-GA Benchmark Report](benchmark-v1.0-ga.md)
  - [DM 1.0-alpha Benchmark Report](benchmark-v1-alpha.md)
- Usage Scenarios
  - [Data Migration Simple Usage Scenario](usage-scenario-simple-migration.md)
  - [Shard Merge Scenario](usage-scenario-shard-merge.md)
  - [Shard Merge Best Practices](shard-merge-best-practices.md)
  - [Switch DM-worker Connection between MySQL Instances](usage-scenario-master-slave-switch.md)
- [Quick Start](get-started.md)
- Deploy
  - Deploy a DM Cluster
    - [Use DM-Ansible](deploy-a-dm-cluster-using-ansible.md)
    - [Use Binary](deploy-a-dm-cluster-using-binary.md)
    - [Use Kubernetes (Experimental)](https://docs.pingcap.com/tidb-in-kubernetes/dev/deploy-tidb-dm)
  - [Migrate Data Using DM](migrate-data-using-dm.md)
- Configure
  - [Overview](config-overview.md)
  - [DM-master Configuration](dm-master-configuration-file.md)
  - [DM-worker Configuration](dm-worker-configuration-file.md)
  - [Full DM-worker Configuration](dm-worker-configuration-file-full.md)
  - [Task Configuration](task-configuration-file.md)
  - [Full Task Configuration](task-configuration-file-full.md)
- Manage the DM Cluster
  - [Cluster Operations](cluster-operations.md)
  - [Cluster Upgrade](dm-upgrade.md)
- Manage Migration Tasks
  - [Manage Tasks](manage-migration-tasks.md)
  - [Precheck Tasks](precheck.md)
  - [Query Task Status](query-status.md)
  - [Skip or Replace Abnormal SQL Statements](skip-or-replace-abnormal-sql-statements.md)
- [Monitor](monitor-a-dm-cluster.md)
- Migrate from MySQL-compatible Database
  - [Migrate from Amazon Aurora](migrate-from-mysql-aurora.md)
- [DM Portal](dm-portal.md)
- Alert
  - [Alert Rules](alert-rules.md)
  - [Handle Alerts](handle-alerts.md)
- Troubleshoot
  - [Handle Errors](error-handling.md)
  - [Handle Performance Issues](handle-performance-issues.md)
- [FAQ](faq.md)
- Releases
  - v1.0
    - [1.0.7](releases/1.0.7.md)
    - [1.0.6](releases/1.0.6.md)
    - [1.0.5](releases/1.0.5.md)
    - [1.0.4](releases/1.0.4.md)
    - [1.0.3](releases/1.0.3.md)
    - [1.0.2](releases/1.0.2.md)
- [TiDB DM Glossary](glossary.md)
