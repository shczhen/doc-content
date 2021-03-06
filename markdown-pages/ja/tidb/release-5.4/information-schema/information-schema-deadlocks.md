---
title: DEADLOCKS
summary: Learn the `DEADLOCKS` information_schema table.
---

# デッドロック {#deadlocks}

`DEADLOCKS`の表は、現在のTiDBノードで最近発生したいくつかのデッドロックエラーの情報を示しています。


```sql
USE information_schema;
DESC deadlocks;
```

```sql
+-------------------------+---------------------+------+------+---------+-------+
| Field                   | Type                | Null | Key  | Default | Extra |
+-------------------------+---------------------+------+------+---------+-------+
| DEADLOCK_ID             | bigint(21)          | NO   |      | NULL    |       |
| OCCUR_TIME              | timestamp(6)        | YES  |      | NULL    |       |
| RETRYABLE               | tinyint(1)          | NO   |      | NULL    |       |
| TRY_LOCK_TRX_ID         | bigint(21) unsigned | NO   |      | NULL    |       |
| CURRENT_SQL_DIGEST      | varchar(64)         | YES  |      | NULL    |       |
| CURRENT_SQL_DIGEST_TEXT | text                | YES  |      | NULL    |       |
| KEY                     | text                | YES  |      | NULL    |       |
| KEY_INFO                | text                | YES  |      | NULL    |       |
| TRX_HOLDING_LOCK        | bigint(21) unsigned | NO   |      | NULL    |       |
+-------------------------+---------------------+------+------+---------+-------+
```

`DEADLOCKS`のテーブルは、複数の行を使用して同じデッドロックイベントを示し、各行には、デッドロックイベントに関連するトランザクションの1つに関する情報が表示されます。 TiDBノードが複数のデッドロックエラーを記録する場合、各エラーは`DEADLOCK_ID`列を使用して区別されます。同じ`DEADLOCK_ID`は、同じデッドロックイベントを示します。 `DEADLOCK_ID`**はグローバルな一意性を保証するものではなく、永続化されないことに**注意してください。同じ結果セットで同じデッドロックイベントのみが表示されます。

`DEADLOCKS`テーブルの各列フィールドの意味は次のとおりです。

-   `DEADLOCK_ID` ：デッドロックイベントのID。テーブルに複数のデッドロックエラーが存在する場合、この列を使用して、さまざまなデッドロックエラーに属する行を区別できます。
-   `OCCUR_TIME` ：デッドロックエラーが発生した時刻。
-   `RETRYABLE` ：デッドロックエラーを再試行できるかどうか。再試行可能なデッドロックエラーの説明については、 [再試行可能なデッドロックエラー](#retryable-deadlock-errors)のセクションを参照してください。
-   `TRY_LOCK_TRX_ID` ：ロックを取得しようとするトランザクションのID。このIDはトランザクションの`start_ts`でもあります。
-   `CURRENT_SQL_DIGEST` ：ロック取得トランザクションで現在実行されているSQLステートメントのダイジェスト。
-   `CURRENT_SQL_DIGEST_TEXT` ：ロック取得トランザクションで現在実行されているSQLステートメントの正規化された形式。
-   `KEY` ：トランザクションがロックしようとするブロックされたキー。このフィールドの値は、16進文字列の形式で表示されます。
-   `KEY_INFO` ： `KEY`の詳細情報。 [KEY_INFO](#key_info)のセクションを参照してください。
-   `TRX_HOLDING_LOCK` ：現在キーのロックを保持していてブロックを引き起こしているトランザクションのID。このIDはトランザクションの`start_ts`でもあります。

<CustomContent platform="tidb">

`DEADLOCKS`のテーブルに記録できるデッドロックイベントの最大数を調整するには、TiDB構成ファイルの[`pessimistic-txn.deadlock-history-capacity`](/tidb-configuration-file.md#deadlock-history-capacity)の構成を調整します。デフォルトでは、最近の10個のデッドロックイベントの情報がテーブルに記録されます。

</CustomContent>

<CustomContent platform="tidb-cloud">

最近の10個のデッドロックイベントの情報が`DEADLOCKS`のテーブルに記録されます。

</CustomContent>

> **警告：**
>
> -   このテーブルを照会できるのは、 [処理する](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_process)の特権を持つユーザーのみです。
> -   `CURRENT_SQL_DIGEST`列の情報（SQLダイジェスト）は、正規化されたSQLステートメントから計算されたハッシュ値です。 `CURRENT_SQL_DIGEST_TEXT`列の情報は、ステートメントの要約テーブルから内部的に照会されるため、対応するステートメントが内部で見つからない可能性があります。 SQLダイジェストとステートメントの要約テーブルの詳細については、 [ステートメント要約表](/statement-summary-tables.md)を参照してください。

## <code>KEY_INFO</code> {#code-key-info-code}

`KEY_INFO`列は、 `KEY`列の詳細情報を示しています。情報はJSON形式で表示されます。各フィールドの説明は次のとおりです。

-   `"db_id"` ：キーが属するスキーマのID。
-   `"db_name"` ：キーが属するスキーマの名前。
-   `"table_id"` ：キーが属するテーブルのID。
-   `"table_name"` ：キーが属するテーブルの名前。
-   `"partition_id"` ：キーが配置されているパーティションのID。
-   `"partition_name"` ：キーが配置されているパーティションの名前。
-   `"handle_type"` ：行キー（つまり、データの行を格納するキー）のハンドルタイプ。可能な値は次のとおりです：
    -   `"int"` ：ハンドルタイプはintです。これは、ハンドルが行IDであることを意味します。
    -   `"common"` ：ハンドルタイプはint64ではありません。このタイプは、クラスター化インデックスが有効になっている場合、非int主キーに表示されます。
    -   `"unknown"` ：ハンドルタイプは現在サポートされていません。
-   `"handle_value"` ：ハンドル値。
-   `"index_id"` ：インデックスキー（インデックスを格納するキー）が属するインデックスID。
-   `"index_name"` ：インデックスキーが属するインデックスの名前。
-   `"index_values"` ：インデックスキーのインデックス値。

上記のフィールドで、フィールドの情報が適用できないか、現在利用できない場合、そのフィールドはクエリ結果で省略されます。たとえば、行キー情報には`index_id` 、および`index_name`は含まれて`index_values`ません。インデックスキーには`handle_type`と`handle_value`が含まれていません。パーティション化されていないテーブルは`partition_id`と`partition_name`を表示しません。削除されたテーブルの`db_name`情報は、 `table_name`などの`db_id`情報を取得できず、テーブルが`index_name`テーブルであるかどうかを区別できません。

> **ノート：**
>
> キーがパーティション化が有効になっているテーブルからのものであり、クエリ中に何らかの理由（たとえば、キーが属するテーブルが削除された）のために、キーが属するスキーマの情報をクエリできない場合、IDキーが属するパーティションの`table_id`フィールドに表示される場合があります。これは、TiDBが複数の独立したテーブルのキーをエンコードするのと同じ方法で、異なるパーティションのキーをエンコードするためです。したがって、スキーマ情報が欠落している場合、TiDBは、キーがパーティション化されていないテーブルに属しているのか、テーブルの1つのパーティションに属しているのかを確認できません。

## 再試行可能なデッドロックエラー {#retryable-deadlock-errors}

<CustomContent platform="tidb-cloud">

> **ノート：**
>
> このセクションは、 TiDB Cloudには適用されません。

</CustomContent>

<CustomContent platform="tidb">

> **ノート：**
>
> `DEADLOCKS`テーブルは、デフォルトでは再試行可能なデッドロックエラーの情報を収集しません。テーブルで再試行可能なデッドロックエラー情報を収集する場合は、TiDB構成ファイルの値[`pessimistic-txn.deadlock-history-collect-retryable`](/tidb-configuration-file.md#deadlock-history-collect-retryable)を調整できます。

</CustomContent>

トランザクションAがトランザクションBによってすでに保持されているロックによってブロックされ、トランザクションBが現在のトランザクションAによって保持されているロックによって直接的または間接的にブロックされている場合、デッドロックエラーが発生します。このデッドロックでは、次の2つの場合があります。

-   ケース1：トランザクションBは、トランザクションAの開始後、トランザクションAがブロックされる前に実行されたステートメントによって生成されたロックによって、（直接的または間接的に）ブロックされる可能性があります。
-   ケース2：トランザクションBは、トランザクションAで現在実行されているステートメントによってもブロックされる可能性があります。

ケース1の場合、TiDBはトランザクションAのクライアントにデッドロックエラーを報告し、トランザクションを終了します。

ケース2の場合、トランザクションAで現在実行されているステートメントは、TiDBで自動的に再試行されます。たとえば、トランザクションAが次のステートメントを実行するとします。


```sql
update t set v = v + 1 where id = 1 or id = 2;
```

トランザクションBは、次の2つのステートメントを連続して実行します。


```sql
update t set v = 4 where id = 2;
update t set v = 2 where id = 1;
```

次に、トランザクションAが2つの行を`id = 1`と`id = 2`でロックし、2つのトランザクションが次の順序で実行される場合：

1.  トランザクションAは行を`id = 1`でロックします。
2.  トランザクションBは最初のステートメントを実行し、行を`id = 2`でロックします。
3.  トランザクションBは2番目のステートメントを実行し、トランザクションAによってブロックされている`id = 1`で行をロックしようとします。
4.  トランザクションAは`id = 2`で行をロックしようとし、デッドロックを形成するトランザクションBによってブロックされます。

この場合、他のトランザクションをブロックするトランザクションAのステートメントも現在実行中のステートメントであるため、現在のステートメントのペシミスティックロックを解決して（トランザクションBを実行し続けることができるように）、現在のステートメントを再試行できます。 。 TiDBは内部でキーハッシュを使用して、これが当てはまるかどうかを判断します。

再試行可能なデッドロックが発生した場合、内部の自動再試行によってトランザクションエラーが発生することはないため、クライアントに対して透過的です。ただし、このような状況が頻繁に発生する場合は、パフォーマンスに影響を与える可能性があります。これが発生すると、TiDBログに`single statement deadlock, retry statement`が表示されます。

## 例1 {#example-1}

テーブル定義と初期データは次のとおりであると想定します。


```sql
create table t (id int primary key, v int);
insert into t values (1, 10), (2, 20);
```

2つのトランザクションが次の順序で実行されます。

| トランザクション1                           | トランザクション2                           | 説明                         |
| ----------------------------------- | ----------------------------------- | -------------------------- |
| `update t set v = 11 where id = 1;` |                                     |                            |
|                                     | `update t set v = 21 where id = 2;` |                            |
| `update t set v = 12 where id = 2;` |                                     | トランザクション1がブロックされます。        |
|                                     | `update t set v = 22 where id = 1;` | トランザクション2はデッドロックエラーを報告します。 |

次に、トランザクション2はデッドロックエラーを報告します。このとき、 `DEADLOCKS`のテーブルをクエリします。


```sql
select * from information_schema.deadlocks;
```

期待される出力は次のとおりです。

```sql
+-------------+----------------------------+-----------+--------------------+------------------------------------------------------------------+-----------------------------------------+----------------------------------------+----------------------------------------------------------------------------------------------------+--------------------+
| DEADLOCK_ID | OCCUR_TIME                 | RETRYABLE | TRY_LOCK_TRX_ID    | CURRENT_SQL_DIGEST                                               | CURRENT_SQL_DIGEST_TEXT                 | KEY                                    | KEY_INFO                                                                                           | TRX_HOLDING_LOCK   |
+-------------+----------------------------+-----------+--------------------+------------------------------------------------------------------+-----------------------------------------+----------------------------------------+----------------------------------------------------------------------------------------------------+--------------------+
|           1 | 2021-08-05 11:09:03.230341 |         0 | 426812829645406216 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000002 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"2"} | 426812829645406217 |
|           1 | 2021-08-05 11:09:03.230341 |         0 | 426812829645406217 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000001 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"1"} | 426812829645406216 |
+-------------+----------------------------+-----------+--------------------+------------------------------------------------------------------+-----------------------------------------+----------------------------------------+----------------------------------------------------------------------------------------------------+--------------------+
```

`DEADLOCKS`つのテーブルに2行のデータが生成されます。両方の行の`DEADLOCK_ID`フィールドは`1`です。これは、両方の行の情報が同じデッドロックエラーに属していることを意味します。最初の行は、 `"7480000000000000355F728000000000000002"`のキーで、 `"426812829645406216"`のトランザクションが`"426812829645406217"`のトランザクションによってブロックされていることを示しています。 2行目は、 `"7480000000000000355F728000000000000001"`のキーで、ID `"426812829645406217"`のトランザクションが`426812829645406216`のトランザクションによってブロックされていることを示しています。これは、相互ブロックを構成し、デッドロックを形成します。

## 例2 {#example-2}

`DEADLOCKS`のテーブルをクエリして、次の結果が得られたとします。

```sql
+-------------+----------------------------+-----------+--------------------+------------------------------------------------------------------+-----------------------------------------+----------------------------------------+----------------------------------------------------------------------------------------------------+--------------------+
| DEADLOCK_ID | OCCUR_TIME                 | RETRYABLE | TRY_LOCK_TRX_ID    | CURRENT_SQL_DIGEST                                               | CURRENT_SQL_DIGEST_TEXT                 | KEY                                    | KEY_INFO                                                                                           | TRX_HOLDING_LOCK   |
+-------------+----------------------------+-----------+--------------------+------------------------------------------------------------------+-----------------------------------------+----------------------------------------+----------------------------------------------------------------------------------------------------+--------------------+
|           1 | 2021-08-05 11:09:03.230341 |         0 | 426812829645406216 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000002 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"2"} | 426812829645406217 |
|           1 | 2021-08-05 11:09:03.230341 |         0 | 426812829645406217 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000001 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"1"} | 426812829645406216 |
|           2 | 2021-08-05 11:09:21.252154 |         0 | 426812832017809412 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000002 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"2"} | 426812832017809413 |
|           2 | 2021-08-05 11:09:21.252154 |         0 | 426812832017809413 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000003 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"3"} | 426812832017809414 |
|           2 | 2021-08-05 11:09:21.252154 |         0 | 426812832017809414 | 22230766411edb40f27a68dadefc63c6c6970d5827f1e5e22fc97be2c4d8350d | update `t` set `v` = ? where `id` = ? ; | 7480000000000000355F728000000000000001 | {"db_id":1,"db_name":"test","table_id":53,"table_name":"t","handle_type":"int","handle_value":"1"} | 426812832017809412 |
+-------------+----------------------------+-----------+--------------------+------------------------------------------------------------------+-----------------------------------------+----------------------------------------+----------------------------------------------------------------------------------------------------+--------------------+
```

上記のクエリ結果の`DEADLOCK_ID`列は、最初の2行が一緒になってデッドロックエラーの情報を表し、互いに待機している2つのトランザクションがデッドロックを形成していることを示しています。次の3行は一緒になって、別のデッドロックエラーの情報を表し、サイクルで待機する3つのトランザクションがデッドロックを形成します。

## CLUSTER_DEADLOCKS {#cluster-deadlocks}

`CLUSTER_DEADLOCKS`テーブルは、クラスタ全体の各TiDBノードでの最近のデッドロックエラーに関する情報を返します。これは、各ノードの`DEADLOCKS`テーブルの情報を組み合わせたものです。 `CLUSTER_DEADLOCKS`には、異なるTiDBノードを区別するためにノードのIPアドレスとポートを表示するための追加の`INSTANCE`列も含まれています。

`DEADLOCK_ID`はグローバルな一意性を保証しないため、 `CLUSTER_DEADLOCKS`テーブルのクエリ結果では、 `INSTANCE`と`DEADLOCK_ID`を一緒に使用して、結果セット内のさまざまなデッドロックエラーの情報を区別する必要があることに注意してください。


```sql
USE information_schema;
DESC cluster_deadlocks;
```

```sql
+-------------------------+---------------------+------+------+---------+-------+
| Field                   | Type                | Null | Key  | Default | Extra |
+-------------------------+---------------------+------+------+---------+-------+
| INSTANCE                | varchar(64)         | YES  |      | NULL    |       |
| DEADLOCK_ID             | bigint(21)          | NO   |      | NULL    |       |
| OCCUR_TIME              | timestamp(6)        | YES  |      | NULL    |       |
| RETRYABLE               | tinyint(1)          | NO   |      | NULL    |       |
| TRY_LOCK_TRX_ID         | bigint(21) unsigned | NO   |      | NULL    |       |
| CURRENT_SQL_DIGEST      | varchar(64)         | YES  |      | NULL    |       |
| CURRENT_SQL_DIGEST_TEXT | text                | YES  |      | NULL    |       |
| KEY                     | text                | YES  |      | NULL    |       |
| KEY_INFO                | text                | YES  |      | NULL    |       |
| TRX_HOLDING_LOCK        | bigint(21) unsigned | NO   |      | NULL    |       |
+-------------------------+---------------------+------+------+---------+-------+
```
