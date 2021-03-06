---
title: online-ddl-scheme 功能介绍
aliases: ['/docs-cn/tidb-data-migration/stable/feature-online-ddl-scheme/','/docs-cn/tidb-data-migration/v1.0/feature-online-ddl-scheme/','/docs-cn/dev/reference/tools/data-migration/features/online-ddl-scheme','/docs-cn/v3.1/reference/tools/data-migration/features/online-ddl-scheme','/docs-cn/v3.0/reference/tools/data-migration/features/online-ddl-scheme','/docs-cn/v2.1/reference/tools/data-migration/features/online-ddl-scheme']
---

# DM online-ddl-scheme

## 概述

DDL 是数据库应用中必然会使用的一类 SQL。MySQL 虽然在 5.6 的版本以后支持了 online-ddl，但是也有或多或少的限制。比如 MDL 锁的获取，某些 DDL 还是需要以 Copy 的方式来进行，在生产业务使用中，DDL 执行过程中的锁表会一定程度上阻塞数据库的读取或者写入。

因此，gh-ost 以及 pt-osc 可以更优雅地在 MySQL 上面执行 DDL，把对读写的影响降到最低。

TiDB 根据 Google F1 的在线异步 schema 变更算法实现，在 DDL 过程中并不会阻塞读写。因此，在 online-schema-change 过程中，gh-ost 和 pt-osc 所产生的大量中间表数据以及 binlog event，在 MySQL 与 TiDB 的数据迁移过程中并不需要。

DM 是 MySQL 到 TiDB 的数据迁移工具，online-ddl-scheme 功能就是对上述两个 online-schema-change 的工具进行特殊的处理，以便更快完成所需的 DDL 迁移。

如果想从源码方面了解 DM online-ddl-scheme，可以参考 [DM 源码阅读系列文章（八）Online Schema Change 迁移支持](https://pingcap.com/blog-cn/dm-source-code-reading-8/#dm-源码阅读系列文章八online-schema-change-迁移支持)

## 配置

online-ddl-scheme 在 task 配置文件里面与 name 同级，例子详见下面配置 Example。完整的配置及意义，可以参考 [DM 完整配置文件示例](task-configuration-file-full.md#完整配置文件示例)：

```yml
# ----------- 全局配置 -----------
## ********* 基本信息配置 *********
name: test                      # 任务名称，需要全局唯一
task-mode: all                  # 任务模式，可设为 "full"、"incremental"、"all"
is-sharding: true               # 是否为分库分表合并任务
meta-schema: "dm_meta"          # 下游储存 `meta` 信息的数据库
remove-meta: false              # 是否在开始运行任务前移除该任务名对应的 `meta`（`checkpoint` 和 `onlineddl` 等）。
enable-heartbeat: false         # 是否开启 `heartbeat` 功能
online-ddl-scheme: "gh-ost"     # 目前仅支持 gh-ost 、pt

target-database:                # 下游数据库实例配置
  host: "192.168.0.1"
  port: 4000
  user: "root"
  password: ""                  # 如果不为空则需经过 dmctl 加密
```

## online-schema-change: gh-ost

gh-ost 在实现 online-schema-change 的过程会产生 3 种 table：

- `gho`：用于应用 DDL，待 `gho` 表中数据同步到与 origin table 一致后，通过 rename 的方式替换 origin table。
- `ghc`：用于存放 online-schema-change 相关的信息。
- `del`：对 origin table 执行 rename 操作而生成。

DM 在迁移过程中会把上述 table 分成 3 类：

- ghostTable : `\_\*\_gho`
- trashTable : `\_\*\_ghc`、`\_\*\_del`
- realTable : 执行 online-ddl 的 origin table

**gh-ost** 涉及的主要 SQL 以及 DM 的处理：

1. 创建 `_ghc` 表：

    ```sql
    Create /* gh-ost */ table `test`.`_test4_ghc` (
                            id bigint auto_increment,
                            last_update timestamp not null DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
                            hint varchar(64) charset ascii not null,
                            value varchar(4096) charset ascii not null,
                            primary key(id),
                            unique key hint_uidx(hint)
                    ) auto_increment=256 ;
    ```

    DM：不执行 `_test4_ghc` 的创建操作。

2. 创建 `_gho` 表：

    ```sql
    Create /* gh-ost */ table `test`.`_test4_gho` like `test`.`test4` ;
    ```

    DM：不执行 `_test4_gho` 的创建操作，根据 ghost_schema、ghost_table 以及 dm_worker 的 `server_id`，删除下游 `dm_meta.{task_name}\_onlineddl` 的记录，清理内存中的相关信息。

    ```sql
    DELETE FROM dm_meta.{task_name}_onlineddl WHERE id = {server_id} and ghost_schema = {ghost_schema} and ghost_table = {ghost_table};
    ```

3. 在 `_gho` 表应用需要执行的 DDL：

    ```sql
    Alter /* gh-ost */ table `test`.`_test4_gho` add column cl1 varchar(20) not null ;
    ```

    DM：不执行 `_test4_gho` 的 DDL 操作，而是把该 DDL 记录到 `dm_meta.{task_name}\_onlineddl` 以及内存中。

    ```sql
    REPLACE INTO dm_meta.{task_name}_onlineddl (id, ghost_schema , ghost_table , ddls) VALUES (......);
    ```

4. 往 `_ghc` 表写入数据，以及往 `_gho` 表同步 origin table 的数据：

    ```sql
    Insert /* gh-ost */ into `test`.`_test4_ghc` values (......);

    Insert /* gh-ost `test`.`test4` */ ignore into `test`.`_test4_gho` (`id`, `date`, `account_id`, `conversion_price`, `ocpc_matched_conversions`, `ad_cost`, `cl2`)
      (select `id`, `date`, `account_id`, `conversion_price`, `ocpc_matched_conversions`, `ad_cost`, `cl2` from `test`.`test4` force index (`PRIMARY`)
        where (((`id` > _binary'1') or ((`id` = _binary'1'))) and ((`id` < _binary'2') or ((`id` = _binary'2')))) lock in share mode
      )   ;
    ```

    DM：只要不是 **realtable** 的 DML 全部不执行。

5. 数据同步完成后 origin table 与 `_gho` 一起改名，完成 online DDL 操作：

    ```sql
    Rename /* gh-ost */ table `test`.`test4` to `test`.`_test4_del`, `test`.`_test4_gho` to `test`.`test4`;
    ```

    DM 执行以下两个操作:

    - 把 rename 语句拆分成两个 SQL：

        ```sql
        rename test.test4 to test._test4_del;
        rename test._test4_gho to test.test4;
        ```

    - 不执行 `rename to _test4_del`。当要执行 `rename ghost_table to origin table` 的时候，并不执行 rename 语句，而是把步骤 3 记录在内存中的 DDL 读取出来，然后把 ghost_table、ghost_schema 替换为 origin_table 以及对应的 schema，再执行替换后的 DDL。

        ```sql
        alter table test._test4_gho add column cl1 varchar(20) not null;
        --替换为
        alter table test.test4 add column cl1 varchar(20) not null;
        ```

> **注意：**
>
> 具体 gh-ost 的 SQL 会根据工具执行时所带的参数而变化。本文只列出主要的 SQL，具体可以参考 [gh-ost 官方文档](https://github.com/github/gh-ost#gh-ost)。

## online-schema-change: pt

pt-osc 在实现 online-schema-change 的过程会产生 2 种 table：

- `new`：用于应用 DDL，待表中数据同步到与 origin table 一致后，再通过 rename 的方式替换 origin table。
- `old`：对 origin table 执行 rename 操作后生成。
- 3 种 **trigger**：`pt_osc\_\*\_ins`、`pt_osc\_\*\_upd`、`pt_osc\_\*\_del`，用于在 pt_osc 过程中，同步 origin table 新产生的数据到 `new`。

DM 在迁移过程中会把上述 table 分成 3 类：

- ghostTable : `\_\*\_new`
- trashTable : `\_\*\_old`
- realTable : 执行的 online-ddl 的 origin table

pt-osc 主要涉及的 SQL 以及 DM 的处理：

1. 创建 `_new` 表：

    ```sql
    CREATE TABLE `test`.`_test4_new` (id int(11) NOT NULL AUTO_INCREMENT,
    date date DEFAULT NULL, account_id bigint(20) DEFAULT NULL, conversion_price decimal(20,3) DEFAULT NULL,  ocpc_matched_conversions bigint(20) DEFAULT NULL, ad_cost decimal(20,3) DEFAULT NULL,cl2 varchar(20) COLLATE utf8mb4_bin NOT NULL,cl1 varchar(20) COLLATE utf8mb4_bin NOT NULL,PRIMARY KEY (id) ) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin ;
    ```

    DM: 不执行 `_test4_new` 的创建操作。根据 ghost_schema、ghost_table 以及 dm_worker 的 `server_id`，删除下游 `dm_meta.{task_name}\_onlineddl` 的记录，清理内存中的相关信息。

    ```sql
    DELETE FROM dm_meta.{task_name}_onlineddl WHERE id = {server_id} and ghost_schema = {ghost_schema} and ghost_table = {ghost_table};
    ```

2. 在 `_new` 表上执行 DDL：

    ```sql
    ALTER TABLE `test`.`_test4_new` add column c3 int;
    ```

    DM: 不执行 `_test4_new` 的 DDL 操作，而是把该 DDL 记录到 `dm_meta.{task_name}\_onlineddl` 以及内存中。

    ```sql
    REPLACE INTO dm_meta.{task_name}_onlineddl (id, ghost_schema , ghost_table , ddls) VALUES (......);
    ```

3. 创建用于同步数据的 3 个 Trigger：

    ```sql
    CREATE TRIGGER `pt_osc_test_test4_del` AFTER DELETE ON `test`.`test4` ...... ;
    CREATE TRIGGER `pt_osc_test_test4_upd` AFTER UPDATE ON `test`.`test4` ...... ;
    CREATE TRIGGER `pt_osc_test_test4_ins` AFTER INSERT ON `test`.`test4` ...... ;
    ```

    DM: 不执行 TiDB 不支持的相关 Trigger 操作。

4. 往 `_new` 表同步 origin table 的数据：

    ```sql
    INSERT LOW_PRIORITY IGNORE INTO `test`.`_test4_new` (`id`, `date`, `account_id`, `conversion_price`, `ocpc_matched_conversions`, `ad_cost`, `cl2`, `cl1`) SELECT `id`, `date`, `account_id`, `conversion_price`, `ocpc_matched_conversions`, `ad_cost`, `cl2`, `cl1` FROM `test`.`test4` LOCK IN SHARE MODE /*pt-online-schema-change 3227 copy table*/
    ```

    DM: 只要不是 **realTable** 的 DML 全部不执行。

5. 数据同步完成后 origin table 与 `_new` 一起改名，完成 online DDL 操作：

    ```sql
    RENAME TABLE `test`.`test4` TO `test`.`_test4_old`, `test`.`_test4_new` TO `test`.`test4`
    ```

    DM 执行以下两个操作:

      - 把 rename 语句拆分成两个 SQL。

         ```sql
         rename test.test4 to test._test4_old;
         rename test._test4_new to test.test4;
         ```

      - 不执行 `rename to _test4_old`。当要执行 `rename ghost_table to origin table` 的时候，并不执行 rename，而是把步骤 2 记录在内存中的 DDL 读取出来，然后把 ghost_table、ghost_schema 替换为 origin_table 以及对应的 schema，再执行替换后的 DDL。

          ```sql
          ALTER TABLE `test`.`_test4_new` add column c3 int;
          --替换为
          ALTER TABLE `test`.`test4` add column c3 int;
          ```

6. 删除 `_old` 表以及 online DDL 的 3 个 Trigger：

    ```sql
    DROP TABLE IF EXISTS `test`.`_test4_old`;
    DROP TRIGGER IF EXISTS `pt_osc_test_test4_del` AFTER DELETE ON `test`.`test4` ...... ;
    DROP TRIGGER IF EXISTS `pt_osc_test_test4_upd` AFTER UPDATE ON `test`.`test4` ...... ;
    DROP TRIGGER IF EXISTS `pt_osc_test_test4_ins` AFTER INSERT ON `test`.`test4` ...... ;
    ```

    DM: 不执行 `_test4_old` 以及 Trigger 的删除操作。

> **注意：**
>
> 具体 pt-osc 的 SQL 会根据工具执行时所带的参数而变化。本文只列出主要的 SQL ，具体可以参考 [pt-osc 官方文档](https://www.percona.com/doc/percona-toolkit/2.2/pt-online-schema-change.html)。
