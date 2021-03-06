---
title: Cached Tables
summary: Learn the cached table feature in TiDB, which is used for rarely-updated small hotspot tables to improve read performance.
---

# キャッシュされたテーブル {#cached-tables}

v6.0.0では、TiDBは、頻繁にアクセスされるがめったに更新されない小さなホットスポットテーブル用のキャッシュテーブル機能を導入しています。この機能を使用すると、テーブル全体のデータがTiDBサーバーのメモリにロードされ、TiDBはTiKVにアクセスせずにメモリからテーブルデータを直接取得するため、読み取りパフォーマンスが向上します。

このドキュメントでは、キャッシュされたテーブルの使用シナリオ、例、および他のTiDB機能との互換性の制限について説明します。

## 使用シナリオ {#usage-scenarios}

キャッシュされたテーブル機能は、次の特性を持つテーブルに適しています。

-   テーブルのデータ量が少ないです。
-   テーブルは読み取り専用であるか、ほとんど更新されません。
-   テーブルは頻繁にアクセスされるため、読み取りパフォーマンスが向上することが期待されます。

テーブルのデータ量が少ないがデータに頻繁にアクセスする場合、データはTiKVのリージョンに集中し、ホットスポットリージョンになり、パフォーマンスに影響します。したがって、キャッシュされたテーブルの一般的な使用シナリオは次のとおりです。

-   アプリケーションが構成情報を読み取るConfiguration / コンフィグレーションテーブル。
-   金融セクターの為替レートの表。これらのテーブルは1日に1回だけ更新されますが、リアルタイムでは更新されません。
-   銀行の支店またはネットワーク情報テーブル。ほとんど更新されません。

例として構成テーブルを取り上げます。アプリケーションが再起動すると、構成情報がすべての接続にロードされるため、読み取りの待ち時間が長くなります。この場合、キャッシュテーブル機能を使用してこの問題を解決できます。

## 例 {#examples}

このセクションでは、キャッシュされたテーブルの使用法を例で説明します。

### 通常のテーブルをキャッシュされたテーブルに設定します {#set-a-normal-table-to-a-cached-table}

テーブル`users`があると仮定します：


```sql
CREATE TABLE users (
    id BIGINT,
    name VARCHAR(100),
    PRIMARY KEY(id)
);
```

このテーブルをキャッシュテーブルに設定するには、次の`ALTER TABLE`ステートメントを使用します。


```sql
ALTER TABLE users CACHE;
```

```sql
Query OK, 0 rows affected (0.01 sec)
```

### キャッシュされたテーブルを確認する {#verify-a-cached-table}

キャッシュされたテーブルを検証するには、 `SHOW CREATE TABLE`ステートメントを使用します。テーブルがキャッシュされている場合、返される結果には次の`CACHED ON`の属性が含まれます。


```sql
SHOW CREATE TABLE users;
```

```sql
+-------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                                                                                                                               |
+-------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| users | CREATE TABLE `users` (
  `id` bigint(20) NOT NULL,
  `name` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`) /*T![clustered_index] CLUSTERED */
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin /* CACHED ON */ |
+-------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

キャッシュされたテーブルからデータを読み取った後、TiDBはデータをメモリにロードします。 `trace`ステートメントを使用して、データがメモリにロードされているかどうかを確認できます。キャッシュがロードされていない場合、返される結果には`regionRequest.SendReqCtx`属性が含まれます。これは、TiDBがTiKVからデータを読み取ることを示します。


```sql
TRACE SELECT * FROM users;
```

```sql
+------------------------------------------------+-----------------+------------+
| operation                                      | startTS         | duration   |
+------------------------------------------------+-----------------+------------+
| trace                                          | 17:47:39.969980 | 827.73µs   |
|   ├─session.ExecuteStmt                        | 17:47:39.969986 | 413.31µs   |
|   │ ├─executor.Compile                         | 17:47:39.969993 | 198.29µs   |
|   │ └─session.runStmt                          | 17:47:39.970221 | 157.252µs  |
|   │   └─TableReaderExecutor.Open               | 17:47:39.970294 | 47.068µs   |
|   │     └─distsql.Select                       | 17:47:39.970312 | 24.729µs   |
|   │       └─regionRequest.SendReqCtx           | 17:47:39.970454 | 189.601µs  |
|   ├─*executor.UnionScanExec.Next               | 17:47:39.970407 | 353.073µs  |
|   │ ├─*executor.TableReaderExecutor.Next       | 17:47:39.970411 | 301.106µs  |
|   │ └─*executor.TableReaderExecutor.Next       | 17:47:39.970746 | 6.57µs     |
|   └─*executor.UnionScanExec.Next               | 17:47:39.970772 | 17.589µs   |
|     └─*executor.TableReaderExecutor.Next       | 17:47:39.970776 | 6.59µs     |
+------------------------------------------------+-----------------+------------+
12 rows in set (0.01 sec)
```

`trace`を再度実行すると、返される結果には`regionRequest.SendReqCtx`属性が含まれなくなります。これは、TiDBがTiKVからデータを読み取るのではなく、代わりにメモリからデータを読み取ることを示します。


```sql
+----------------------------------------+-----------------+------------+
| operation                              | startTS         | duration   |
+----------------------------------------+-----------------+------------+
| trace                                  | 17:47:40.533888 | 453.547µs  |
|   ├─session.ExecuteStmt                | 17:47:40.533894 | 402.341µs  |
|   │ ├─executor.Compile                 | 17:47:40.533903 | 205.54µs   |
|   │ └─session.runStmt                  | 17:47:40.534141 | 132.084µs  |
|   │   └─TableReaderExecutor.Open       | 17:47:40.534202 | 14.749µs   |
|   ├─*executor.UnionScanExec.Next       | 17:47:40.534306 | 3.21µs     |
|   └─*executor.UnionScanExec.Next       | 17:47:40.534316 | 1.219µs    |
+----------------------------------------+-----------------+------------+
7 rows in set (0.00 sec)
```

キャッシュされたテーブルの読み取りには`UnionScan`演算子が使用されるため、キャッシュされたテーブルの実行プランで`UnionScan`を`explain`まで確認できます。


```sql
+-------------------------+---------+-----------+---------------+--------------------------------+
| id                      | estRows | task      | access object | operator info                  |
+-------------------------+---------+-----------+---------------+--------------------------------+
| UnionScan_5             | 1.00    | root      |               |                                |
| └─TableReader_7         | 1.00    | root      |               | data:TableFullScan_6           |
|   └─TableFullScan_6     | 1.00    | cop[tikv] | table:users   | keep order:false, stats:pseudo |
+-------------------------+---------+-----------+---------------+--------------------------------+
3 rows in set (0.00 sec)
```

### キャッシュされたテーブルにデータを書き込む {#write-data-to-a-cached-table}

キャッシュされたテーブルはデータ書き込みをサポートします。たとえば、 `users`のテーブルにレコードを挿入できます。


```sql
INSERT INTO users(id, name) VALUES(1001, 'Davis');
```

```sql
Query OK, 1 row affected (0.00 sec)
```


```sql
SELECT * FROM users;
```

```sql
+------+-------+
| id   | name  |
+------+-------+
| 1001 | Davis |
+------+-------+
1 row in set (0.00 sec)
```

> **ノート：**
>
> キャッシュされたテーブルにデータを挿入すると、第2レベルの書き込みレイテンシが発生する可能性があります。レイテンシーは、グローバル環境変数[`tidb_table_cache_lease`](/system-variables.md#tidb_table_cache_lease-new-in-v600)によって制御されます。アプリケーションに基づいてレイテンシが許容できるかどうかを確認することで、キャッシュテーブル機能を使用するかどうかを決定できます。たとえば、読み取り専用のシナリオでは、値を`tidb_table_cache_lease`に増やすことができます。
>
> >
> ```sql
> set @@global.tidb_table_cache_lease = 10;
> ```
>
> キャッシュされたテーブルの機能は、キャッシュごとにリースを設定する必要がある複雑なメカニズムで実装されているため、キャッシュされたテーブルの書き込みレイテンシは高くなります。複数のTiDBインスタンスがある場合、1つのインスタンスは、他のインスタンスがデータをキャッシュしているかどうかを認識しません。インスタンスがテーブルデータを直接変更する場合、他のインスタンスは古いキャッシュデータを読み取ります。正確性を確保するために、キャッシュテーブルの実装では、リースメカニズムを使用して、リースの期限が切れる前にデータが変更されないようにします。そのため、書き込みレイテンシが高くなります。

### キャッシュされたテーブルを通常のテーブルに戻します {#revert-a-cached-table-to-a-normal-table}

> **ノート：**
>
> キャッシュされたテーブルでのDDLステートメントの実行は失敗します。キャッシュされたテーブルでDDLステートメントを実行する前に、まずキャッシュ属性を削除し、キャッシュされたテーブルを通常のテーブルに戻す必要があります。


```sql
TRUNCATE TABLE users;
```

```sql
ERROR 8242 (HY000): 'Truncate Table' is unsupported on cache tables.
```


```sql
mysql> ALTER TABLE users ADD INDEX k_id(id);
```

```sql
ERROR 8242 (HY000): 'Alter Table' is unsupported on cache tables.
```

キャッシュされたテーブルを通常のテーブルに戻すには、 `ALTER TABLE t NOCACHE`を使用します。


```sql
ALTER TABLE users NOCACHE
```

```sql
Query OK, 0 rows affected (0.00 sec)
```

## キャッシュされたテーブルのサイズ制限 {#size-limit-of-cached-tables}

TiDBはテーブル全体のデータをメモリにロードし、キャッシュされたデータは変更後に無効になり、再ロードする必要があるため、キャッシュされたテーブルは小さなテーブルのシナリオにのみ適しています。

現在、キャッシュされたテーブルのサイズ制限は、TiDBでは64MBです。テーブルデータが64MBを超える場合、 `ALTER TABLE t CACHE`の実行は失敗します。

## 他のTiDB機能との互換性の制限 {#compatibility-restrictions-with-other-tidb-features}

キャッシュされたテーブルは、次の機能をサポートしてい**ません**。

-   パーティションテーブルでの`ALTER TABLE t ADD PARTITION`操作の実行はサポートされていません。
-   一時テーブルでの`ALTER TABLE t CACHE`操作の実行はサポートされていません。
-   ビューで`ALTER TABLE t CACHE`操作を実行することはサポートされていません。
-   StaleReadはサポートされていません。
-   キャッシュされたテーブルでの直接DDL操作はサポートされていません。 DDL操作を実行する前に、まず`ALTER TABLE t NOCACHE`を使用して、キャッシュされたテーブルを通常のテーブルに戻す必要があります。

キャッシュされたテーブルは、次のシナリオでは使用でき**ません**。

-   履歴データを読み取るようにシステム変数`tidb_snapshot`を設定します。
-   変更中は、データが再ロードされるまで、キャッシュされたデータは無効になります。

## TiDB移行ツールとの互換性 {#compatibility-with-tidb-migration-tools}

キャッシュされたテーブルは、MySQL構文のTiDB拡張です。 TiDBのみが`ALTER TABLE ... CACHE`のステートメントを認識できます。 TiDB移行ツールは、バックアップと復元（BR）、TiCDC、Dumplingなどのキャッシュテーブルをサポートしてい**ません**。これらのツールは、キャッシュされたテーブルを通常のテーブルとして扱います。

つまり、キャッシュされたテーブルをバックアップして復元すると、通常のテーブルになります。ダウンストリームクラスタが別のTiDBクラスタであり、キャッシュテーブル機能を引き続き使用する場合は、ダウンストリームテーブルで`ALTER TABLE ... CACHE`を実行することにより、ダウンストリームクラスタでキャッシュテーブルを手動で有効にできます。

## も参照してください {#see-also}

-   [他の机](/sql-statements/sql-statement-alter-table.md)
-   [システム変数](/system-variables.md)
