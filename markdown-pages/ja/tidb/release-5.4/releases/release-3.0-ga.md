---
title: TiDB 3.0 GA Release Notes
---

# TiDB3.0GAリリースノート {#tidb-3-0-ga-release-notes}

発売日：2019年6月28日

TiDBバージョン：3.0.0

TiDB Ansibleバージョン：3.0.0

## 概要 {#overview}

2019年6月28日、TiDB3.0GAがリリースされました。対応するTiDBAnsibleのバージョンは3.0.0です。 TiDB 2.1と比較して、このリリースでは次の点で大幅に改善されています。

-   安定。 TiDB 3.0は、最大150以上のノードと300以上のTBのストレージを備えた大規模クラスターで長期的な安定性を実証しています。
-   使いやすさ。 TiDB 3.0は、標準化された低速クエリログ、十分に開発されたログファイル仕様、ユーザーの運用コストを節約する`EXPLAIN ANALYZE`やSQL Traceなどの新機能など、使いやすさを多面的に改善しています。
-   パフォーマンス。 TiDB 3.0のパフォーマンスは、TPC-CベンチマークではTiDB 2.1の4.5倍、Sysbenchベンチマークでは1.5倍以上です。ビューのサポートのおかげで、TPC-H50GQ15を正常に実行できるようになりました。
-   ウィンドウ関数、ビュー（ **Experimental** ）、パーティションテーブル、プラグインフレームワーク、ペシミスティックロック（ <strong>Experimental</strong> ）、 `SQL Plan Management`などの新機能。

## TiDB {#tidb}

-   新機能
    -   ウィンドウ関数をサポートします。 `NTILE` `RANK` `PERCENT_RANK` `LEAD`の`CUME_DIST`の`NTH_VALUE` `LAST_VALUE`と`FIRST_VALUE` `DENSE_RANK`が`ROW_NUMBER` `LAG`
    -   サポートビュー（**実験的**）
    -   テーブルパーティションを改善する
        -   範囲パーティションのサポート
        -   ハッシュパーティションをサポートする
    -   プラグインフレームワークを追加し、IPホワイトリスト（ **Enterprise** ）や監査ログ（ <strong>Enterprise</strong> ）などのプラグインをサポートします。
    -   SQLプラン管理機能をサポートしてSQL実行プランバインディングを作成し、クエリの安定性を確保します（**実験的**）
-   SQLオプティマイザー
    -   `NOT EXISTS`のサブクエリを最適化し、それを`Anti Semi Join`に変換して、パフォーマンスを向上させます
    -   `Outer Join`の定数伝播を最適化し、 `Outer Join`の除去の最適化ルールを追加して、効果のない計算を減らし、パフォーマンスを向上させます。
    -   `IN`のサブクエリを最適化して、集計後に`Inner Join`を実行し、パフォーマンスを向上させます
    -   より多くのシナリオに適応するために`Index Join`を最適化する
    -   RangePartitionのPartitionPruning最適化ルールを改善します
    -   全表スキャンを回避し、パフォーマンスを向上させるために、クエリロジックを`_tidb_rowid`に最適化します
    -   パフォーマンスを向上させるためにフィルターに関連する列がある場合は、複合インデックスのアクセス条件を抽出するときに、インデックスのより多くのプレフィックス列を一致させます
    -   列間の順序相関を使用して、コスト見積もりの精度を向上させます
    -   欲張り戦略と動的計画法アルゴリズムに基づいて`Join Order`を最適化し、複数のテーブルの結合操作を高速化します
    -   クエリの安定性を向上させるために実行プランが統計に過度に依存することを防ぐためのいくつかのルールを使用して、スカイラインプルーニングをサポートします
    -   NULL値を持つ単一列インデックスの行数推定の精度を向上させます
    -   全表スキャンを回避し、統計収集によるパフォーマンスを向上させるために、各リージョンでランダムにサンプリングする`FAST ANALYZE`をサポートします。
    -   統計収集のパフォーマンスを向上させるために、単調に増加するインデックス列での増分分析操作をサポートします
    -   `DO`ステートメントでのサブクエリの使用のサポート
    -   トランザクションで`Index Join`を使用することをサポート
    -   パラメータなしの`execute`ステートメントをサポートするように`prepare`を最適化する
    -   `stats-lease`の変数値が0の場合に、統計を自動ロードするようにシステムの動作を変更します
    -   履歴統計のエクスポートをサポート
    -   ヒストグラムの`dump`相関をサポートし`load`
-   SQL実行エンジン
    -   ログ出力の最適化： `EXECUTE`つはユーザー変数を出力し、 `COMMIT`はトラブルシューティングを容易にするための低速クエリログを出力します
    -   SQLチューニングの使いやすさを向上させる`EXPLAIN ANALYZE`の関数をサポートする
    -   次の行のIDを取得するための`admin show next_row_id`コマンドをサポートします
    -   `COALESCE`つの組み込み関数を追加し`JSON_ARRAY_APPEND` `JSON_MERGE_PRESERVE` `JSON_QUOTE` 、 `NAME_CONST` `BENCHMARK`
    -   チャンクサイズの制御ロジックを最適化して、クエリコンテキストに基づいて動的に調整し、SQLの実行時間とリソースの消費を削減します
    -   `IndexReader` `TableReader`および`IndexLookupReader` ）でのメモリー使用量の追跡と制御をサポートします
    -   空の`ON`条件をサポートするように、マージ結合演算子を最適化します
    -   列が多すぎる単一のテーブルの書き込みパフォーマンスを最適化する
    -   逆順のスキャンデータをサポートすることにより、 `admin show ddl jobs`のパフォーマンスを向上させます
    -   ホットスポットの問題を軽減するために、テーブルRegionを手動で分割する`split table region`のステートメントを追加します
    -   ホットスポットの問題を軽減するためにインデックスリージョンを手動で分割する`split index region`のステートメントを追加します
    -   式をコプロセッサーにプッシュダウンすることを禁止するブロックリストを追加します
    -   `Expensive Query`のログを最適化して、構成された実行時間またはメモリの制限を超えたときにSQLクエリをログに出力します
-   DDL
    -   文字セット`utf8`から`utf8mb4`への移行をサポート
    -   デフォルトの文字セットを`utf8`から`utf8mb4`に変更します
    -   `alter schema`ステートメントを追加して、データベースの文字セットと照合順序を変更します
    -   `INSTANT`アルゴリズム`INPLACE`をサポート
    -   サポート`SHOW CREATE VIEW`
    -   サポート`SHOW CREATE USER`
    -   誤って削除されたテーブルの高速リカバリをサポート
    -   ADDINDEXの同時実行数の動的な調整をサポート
    -   `CREATE TABLE`ステートメントを使用してテーブルを作成するときにリージョンを事前に割り当てる`pre_split_regions`オプションを追加して、テーブル作成後の大量の書き込みによって引き起こされる書き込みホットリージョンを軽減します。
    -   ホットスポットの問題を軽減するためにSQLステートメントを使用して指定されたテーブルのインデックスと範囲によるリージョンの分割をサポート
    -   `ddl_error_count_limit`のグローバル変数を追加して、DDLタスクの再試行回数を制限します
    -   ホットスポットの問題を軽減するために、列にAUTO_INCREMENT属性が含まれている場合に、 `SHARD_ROW_ID_BITS`を使用して行IDを分散する機能を追加します。
    -   無効なDDLメタデータの存続期間を最適化して、TiDBクラスタのアップグレード後のDDL操作の通常の実行の回復を高速化します
-   トランザクション
    -   悲観的なトランザクションモードをサポートする（**実験的**）
    -   トランザクション処理ロジックを最適化して、より多くのシナリオに適応させます。
        -   デフォルト値の`tidb_disable_txn_auto_retry`を`on`に変更します。これは、自動コミットされていないトランザクションが再試行されないことを意味します。
        -   `tidb_batch_commit`のシステム変数を追加して、トランザクションを複数のトランザクションに分割し、同時に実行します
        -   `tidb_low_resolution_tso`のシステム変数を追加して、バッチで取得するTSOの数を制御し、トランザクションがTSOを要求する回数を減らして、整合性の要件が比較的低いシナリオでのパフォーマンスを向上させます。
        -   分離レベルがSERIALIZABLEに設定されている場合にエラーを報告するかどうかを制御するために、 `tidb_skip_isolation_level_check`の変数を追加します
        -   `tidb_disable_txn_auto_retry`のシステム変数を変更して、再試行可能なすべてのエラーで機能するようにします
-   権限管理
    -   `ANALYZE` 、および`SET GLOBAL`ステートメントの権限チェックを`SHOW PROCESSLIST`し`USE`
    -   ロールベースのアクセス制御（RBAC）をサポート（**実験的**）
-   サーバ
    -   遅いクエリログを最適化する：
        -   ログ形式を再構築する
        -   ログの内容を最適化する
        -   ログクエリメソッドを最適化して、メモリテーブルの`INFORMATION_SCHEMA.SLOW_QUERY`ステートメントと`ADMIN SHOW SLOW`ステートメントを使用して、低速のクエリログをクエリできるようにします。
    -   ツールによる収集と分析を容易にするために、再構築されたログシステムを使用して統一されたログ形式の仕様を開発します
    -   SQLステートメントを使用したTiDBBinlogサービスの管理をサポートします。これには、ステータスのクエリ、TiDB Binlogの有効化、TiDBBinlog戦略の維持と送信が含まれます。
    -   `unix_socket`を使用してデータベースに接続することをサポート
    -   SQLステートメントのサポート`Trace`
    -   トラブルシューティングを容易にするために、1HTTPインターフェースを介した`/debug/zip`インスタンスの情報の取得をサポートします。
    -   トラブルシューティングを容易にするために監視項目を最適化します。
        -   `high_error_rate_feedback_total`の監視項目を追加して、統計に基づいて実際のデータ量と推定データ量の差を監視します。
        -   データベースディメンションにQPS監視項目を追加します
    -   システム初期化プロセスを最適化して、DDL所有者のみが初期化を実行できるようにします。これにより、初期化またはアップグレードの起動時間が短縮されます。
    -   `kill query`の実行ロジックを最適化して、パフォーマンスを向上させ、リソースが適切に解放されるようにします。
    -   スタートアップオプション`config-check`を追加して、構成ファイルの有効性を確認します
    -   `tidb_back_off_weight`のシステム変数を追加して、内部エラー再試行のバックオフ時間を制御します
    -   `wait_timeout`と`interactive_timeout`のシステム変数を追加して、許可される最大アイドル接続を制御します
    -   TiKVの接続プールを追加して、接続確立時間を短縮します
-   互換性
    -   `ALLOW_INVALID_DATES`モードをサポートする
    -   MySQL320ハンドシェイクプロトコルをサポートする
    -   符号なしBIGINT列を自動インクリメント列として明示することをサポート
    -   `SHOW CREATE DATABASE IF NOT EXISTS`構文をサポートする
    -   CSVファイルのフォールトトレランス`load data`を最適化する
    -   フィルタリング条件にユーザー変数が含まれている場合は、述語プッシュダウン操作を破棄して、ユーザー変数を使用してウィンドウ関数をシミュレートするMySQLの動作との互換性を向上させます。

## PD {#pd}

-   単一ノードからのクラスタの再作成をサポート
-   リージョンメタデータをetcdからgo-leveldbストレージエンジンに移行して、大規模クラスターのetcdのストレージボトルネックを解決します
-   API
    -   `remove-tombstone`のAPIを追加してTombstoneストアをクリアします
    -   リージョン情報をバッチクエリするために`ScanRegions`のAPIを追加します
    -   実行中の演算子をクエリするために`GetOperator`のAPIを追加します
    -   `GetStores`のパフォーマンスを最適化する
-   構成
    -   構成アイテムのエラーを回避するために構成チェックロジックを最適化する
    -   リージョンマージの方向を制御するには、 `enable-two-way-merge`を追加します
    -   ホットリージョンのスケジューリングレートを制御するには、 `hot-region-schedule-limit`を追加します
    -   複数のしきい値に連続して到達したときにホットスポットを識別するには、 `hot-region-cache-hits-threshold`を追加します
    -   `store-balance-rate`の構成アイテムを追加して、1分あたりに許可されるバランスリージョンオペレーターの最大数を制御します
-   スケジューラの最適化
    -   各店舗のオペレーターの速度を個別に制御するための店舗制限メカニズムを追加します
    -   `waitingOperator`のキューをサポートして、さまざまなスケジューラ間のリソース競合を最適化します
    -   スケジューリング操作をTiKVにアクティブに送信するためのスケジューリングレート制限をサポートします。これにより、単一ノードでの同時スケジューリングタスクの数が制限され、スケジューリングレートが向上します。
    -   制限メカニズムによって制限されないように`Region Scatter`のスケジューリングを最適化します
    -   ホットスポットのスケジューリングが不十分なシナリオでTiKVの安定性テストを容易にするために、 `shuffle-hot-region`のスケジューラーを追加します
-   シミュレーター
    -   データインポートシナリオ用のシミュレーターを追加する
    -   ストアの異なるハートビート間隔の設定をサポート
-   その他
    -   etcdをアップグレードして、一貫性のないログ出力形式、prevoteでのリーダー選択の失敗、およびリースのデッドロックの問題を解決します。
    -   ツールによる収集と分析を容易にするために、再構築されたログシステムを使用して統一されたログ形式の仕様を開発します
    -   スケジューリングパラメータ、クラスタラベル情報、TSO要求を処理するためにPDが消費する時間、ストアIDとアドレス情報などを含む監視メトリックを追加します。

## TiKV {#tikv}

-   分散GCと同時ロック解決をサポートして、GCのパフォーマンスを向上させます
-   サポートは`raw_scan`と`raw_batch_scan`を逆にしました
-   マルチスレッドRaftstoreとマルチスレッドApplyをサポートして、単一ノード内のスケーラビリティ、同時実行容量、およびリソース使用量を改善します。同じレベルの圧力でパフォーマンスが70％向上します
-   Raftメッセージのバッチ送受信をサポートし、書き込みが集中するシナリオでTPSを7％向上させます
-   書き込みストールを回避するために、スナップショットを適用する前にRocksDBレベル0ファイルをチェックすることをサポートします
-   値のサイズが1KiBを超えるシナリオの書き込みパフォーマンスを向上させ、書き込みの増幅をある程度緩和するKey-ValueプラグインであるTitanを紹介します
-   悲観的なトランザクションモードをサポートする（**実験的**）
-   HTTP経由での監視情報の取得をサポート
-   `Insert`のセマンティクスを変更して、キーがない場合にのみプリライトが成功するようにします。
-   ツールによる収集と分析を容易にするために、再構築されたログシステムを使用して統一されたログ形式の仕様を開発します
-   構成情報とキーバウンドクロッシングに関連するパフォーマンスメトリックを追加します
-   RawKVでローカルリーダーをサポートしてパフォーマンスを向上させる
-   エンジン
    -   メモリ管理を最適化して、 `Iterator Key Bound Option`のメモリ割り当てとコピーを削減します
    -   異なる列ファミリー間での`block cache`の共有をサポート
-   サーバ
    -   コンテキストスイッチのオーバーヘッドを`batch commands`から削減
    -   `txn scheduler`を削除します
    -   `read index`と`GC worker`に関連する監視項目を追加します
-   RaftStore
    -   RaftStoreからのCPU消費を最適化するためにHibernateリージョンをサポートする（**実験的**）
    -   ローカルリーダースレッドを削除します
-   コプロセッサー
    -   計算フレームワークをリファクタリングしてベクトル演算子、ベクトル式を使用した計算、およびベクトル集計を実装してパフォーマンスを向上させる
    -   TiDBの`EXPLAIN ANALYZE`ステートメントの演算子実行ステータスの提供をサポート
    -   コンテキストスイッチのコストを削減するには、 `work-stealing`スレッドプールモデルに切り替えます

## ツール {#tools}

-   TiDB Lightning
    -   データテーブルのリダイレクトレプリケーションをサポート
    -   CSVファイルのインポートをサポート
    -   SQLからKVペアへの変換のパフォーマンスを向上させる
    -   単一テーブルのバッチインポートをサポートして、パフォーマンスを向上させます
    -   TiKV-importerのパフォーマンスを向上させるために、大きなテーブルのデータとインデックスを個別にインポートすることをサポートします
    -   新しいファイルで列データが欠落している場合に、 `row_id`またはデフォルトの列値を使用して欠落している列を埋めることをサポートします
    -   SSTファイルをTiKVにアップロードするときに速度制限を`TIKV-importer`に設定することをサポート
-   TiDB Binlog
    -   コンテナ環境でブリッジモードをサポートするには、Drainerに`advertise-addr`の構成を追加します
    -   Pumpに`GetMvccByEncodeKey`関数を追加して、トランザクションステータスのクエリを高速化します
    -   コンポーネント間の通信データの圧縮をサポートして、ネットワークリソースの消費を削減します
    -   Kafkaからのbinlogの読み取りをサポートするアービターツールを追加し、データをMySQLに複製します
    -   Reparoを介したレプリケーションを必要としないファイルの除外をサポート
    -   生成された列の複製をサポート
    -   DDLクエリを解析するためのさまざまなSQLモードの使用をサポートする`syncer.sql-mode`の構成アイテムを追加します
    -   複製されないフィルタリングテーブルをサポートするために`syncer.ignore-table`の構成アイテムを追加します
-   sync-diff-inspector
    -   チェックポイントをサポートして検証ステータスを記録し、再起動後に最後に保存したポイントから検証を続行します
    -   チェックサムを計算してデータの整合性をチェックするには、 `only-use-checksum`の構成アイテムを追加します
    -   より多くのシナリオに適応するための比較のためにチャンクを分割するためのTiDB統計と複数の列の使用をサポート

## TiDB Ansible {#tidb-ansible}

-   次の監視コンポーネントを安定バージョンにアップグレードします。
    -   V2.2.1からV2.8.1へのプロメテウス
    -   V0.4.0からV0.7.0へのプッシュゲートウェイ
    -   V0.15.2からV0.17.0へのNode_exporter
    -   V0.14.0からV0.17.0へのAlertmanager
    -   V4.6.3からV6.1.6へのGrafana
    -   V2.5.14からV2.7.11までAnsible
-   TiKVサマリーモニタリングダッシュボードを追加して、クラスタのステータスを便利に表示します
-   TiKVのtrouble_shooting監視ダッシュボードを追加して、重複するアイテムを削除し、トラブルシューティングを容易にします
-   TiKV詳細監視ダッシュボードを追加して、デバッグとトラブルシューティングを容易にします
-   更新のパフォーマンスを向上させるために、更新のローリング中にバージョンの整合性の同時チェックを追加します
-   TiDBLightningの展開と運用をサポートする
-   `table-regions.py`のスクリプトを最適化して、テーブルによるリーダー分布の表示をサポートします
-   TiDBモニタリングを最適化し、SQLカテゴリごとにレイテンシ関連のモニタリング項目を追加します
-   オペレーティングシステムのバージョン制限を変更して、CentOS7.0以降およびRedHat7.0以降のオペレーティングシステムのみをサポートするようにします。
-   監視項目を追加して、クラスタの最大QPSを予測します（デフォルトでは非表示）
