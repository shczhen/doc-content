---
title: TiDB 2.1 RC1 Release Notes
---

# TiDB2.1RC1リリースノート {#tidb-2-1-rc1-release-notes}

2018年8月24日、TiDB 2.1 RC1がリリースされました！ TiDB 2.1ベータ版と比較して、このリリースでは、安定性、SQLオプティマイザー、統計情報、および実行エンジンが大幅に改善されています。

## TiDB {#tidb}

-   SQLオプティマイザー
    -   相関サブクエリが非相関化された後に間違った結果が返される問題を修正します[＃6972](https://github.com/pingcap/tidb/pull/6972)
    -   [＃7011](https://github.com/pingcap/tidb/pull/7011)の`Explain`結果を[＃7041](https://github.com/pingcap/tidb/pull/7041)化する
    -   `IndexJoin`の外部テーブルの選択戦略を[＃7019](https://github.com/pingcap/tidb/pull/7019)化する
    -   `PREPARE`以外のステートメントのプランキャッシュを削除します[＃7040](https://github.com/pingcap/tidb/pull/7040)
    -   `INSERT`ステートメントが解析および実行されない場合があるという問題を修正します[＃7068](https://github.com/pingcap/tidb/pull/7068)
    -   `IndexJoin`の結果が正しくない場合があるという問題を修正します[＃7150](https://github.com/pingcap/tidb/pull/7150)
    -   場合によっては、一意のインデックスを使用して`NULL`の値が見つからないという問題を修正します[＃7163](https://github.com/pingcap/tidb/pull/7163)
    -   UTF [＃7194](https://github.com/pingcap/tidb/pull/7194) 81のプレフィックスインデックスの範囲計算の問題を修正します。
    -   場合によっては`Project`演算子を削除することにより、結果が正しくないという問題を修正します[＃7257](https://github.com/pingcap/tidb/pull/7257)
    -   主キーが整数[＃7316](https://github.com/pingcap/tidb/pull/7316)の場合に`USE INDEX(PRIMARY)`を使用できない問題を修正します
    -   場合によっては、相関列を使用してインデックス範囲を計算できない問題を修正します[＃7357](https://github.com/pingcap/tidb/pull/7357)
-   SQL実行エンジン
    -   夏時間が正しく計算されない場合があるという問題を修正します[＃6823](https://github.com/pingcap/tidb/pull/6823)
    -   集計関数フレームワークをリファクタリングして、 `Stream`および`Hash`の集計演算子の実行効率を向上させます[＃6852](https://github.com/pingcap/tidb/pull/6852)
    -   `Hash`集計演算子が正常に終了できない場合があるという問題を修正します[＃6982](https://github.com/pingcap/tidb/pull/6982)
    -   `BIT_OR`が非整数データを正しく[＃6994](https://github.com/pingcap/tidb/pull/6994)しない問題を修正し`BIT_AND` `BIT_XOR`
    -   `REPLACE INTO`ステートメントの実行速度を最適化し、パフォーマンスをほぼ10倍に向上させます[＃7027](https://github.com/pingcap/tidb/pull/7027)
    -   時間タイプデータのメモリ使用量を最適化し、時間タイプデータのメモリ使用量を50％削減します[＃7043](https://github.com/pingcap/tidb/pull/7043)
    -   `UNION`ステートメントで返された結果が符号付き整数および符号なし整数と混合される問題を修正します[＃7112](https://github.com/pingcap/tidb/pull/7112)と互換性がありません。
    -   `FROM_BASE64` `RPAD` `LPAD` [＃7171](https://github.com/pingcap/tidb/pull/7171) `REPEAT`された[＃7409](https://github.com/pingcap/tidb/pull/7409)が多[＃7266](https://github.com/pingcap/tidb/pull/7266)ことによって[＃7431](https://github.com/pingcap/tidb/pull/7431)れるpanicの問題を修正し`TO_BASE64`
    -   `MergeJoin`が`IndexJoin`の値を処理するときの誤った結果を修正し`NULL` [＃7255](https://github.com/pingcap/tidb/pull/7255)
    -   場合によっては`Outer Join`の誤った結果を修正します[＃7288](https://github.com/pingcap/tidb/pull/7288)
    -   `Data Truncated`のエラーメッセージを改善して、表[＃7401](https://github.com/pingcap/tidb/pull/7401)の間違ったデータと対応するフィールドを見つけやすくします。
    -   場合によっては`decimal`の[＃7208](https://github.com/pingcap/tidb/pull/7208)た結果を修正し[＃7001](https://github.com/pingcap/tidb/pull/7001) [＃7113](https://github.com/pingcap/tidb/pull/7113) [＃7202](https://github.com/pingcap/tidb/pull/7202)
    -   ポイント選択のパフォーマンスを最適化する[＃6937](https://github.com/pingcap/tidb/pull/6937)
    -   根本的な問題を回避するために、分離レベル`Read Committed`を禁止します[＃7211](https://github.com/pingcap/tidb/pull/7211)
    -   場合[＃7291](https://github.com/pingcap/tidb/pull/7291)は`LTRIM`の誤った結果を修正し`RTRIM` `TRIM`
    -   `MaxOneRow`演算子は、返された結果が1行を超えないことを保証できないという問題を修正します[＃7375](https://github.com/pingcap/tidb/pull/7375)
    -   範囲が多すぎるコプロセッサー要求を分割する[＃7454](https://github.com/pingcap/tidb/pull/7454)
-   統計
    -   統計動的収集のメカニズムを最適化する[＃6796](https://github.com/pingcap/tidb/pull/6796)
    -   データが頻繁に更新されるときに`Auto Analyze`が機能しないという問題を修正します[＃7022](https://github.com/pingcap/tidb/pull/7022)
    -   統計の動的更新プロセス中の書き込みの競合を減らす[＃7124](https://github.com/pingcap/tidb/pull/7124)
    -   統計が正しくない場合のコスト見積もりを最適化する[＃7175](https://github.com/pingcap/tidb/pull/7175)
    -   `AccessPath`コスト見積もり戦略を最適化する[＃7233](https://github.com/pingcap/tidb/pull/7233)
-   サーバ
    -   特権情報をロードする際のバグを修正する[＃6976](https://github.com/pingcap/tidb/pull/6976)
    -   特権チェック[＃6954](https://github.com/pingcap/tidb/pull/6954)で`Kill`コマンドが厳しすぎる問題を修正します
    -   一部の2進数タイプを削除する問題を修正[＃6922](https://github.com/pingcap/tidb/pull/6922)
    -   出力ログを短くする[＃7029](https://github.com/pingcap/tidb/pull/7029)
    -   `mismatchClusterID`の問題を処理する[＃7053](https://github.com/pingcap/tidb/pull/7053)
    -   `advertise-address`の構成アイテム[＃7078](https://github.com/pingcap/tidb/pull/7078)を追加します
    -   `GrpcKeepAlive`のオプションを追加します[＃7100](https://github.com/pingcap/tidb/pull/7100)
    -   接続または`Token`回のモニターを追加します[＃7110](https://github.com/pingcap/tidb/pull/7110)
    -   データデコードパフォーマンスを最適化する[＃7149](https://github.com/pingcap/tidb/pull/7149)
    -   35に`PROCESSLIST` [＃7236](https://github.com/pingcap/tidb/pull/7236)テーブルを追加し`INFORMMATION_SCHEMA`
    -   特権の検証で複数のルールがヒットした場合の順序の問題を修正します[＃7211](https://github.com/pingcap/tidb/pull/7211)
    -   エンコーディング関連のシステム変数のデフォルト値をUTF [＃7198](https://github.com/pingcap/tidb/pull/7198) 81に変更します。
    -   遅いクエリログに詳細情報を表示させる[＃7302](https://github.com/pingcap/tidb/pull/7302)
    -   PDへのtidb-server関連情報の登録と[＃7082](https://github.com/pingcap/tidb/pull/7082)によるこの情報の取得をサポートします。
-   互換性
    -   セッション変数`warning_count`および[＃6945](https://github.com/pingcap/tidb/pull/6945)をサポートし`error_count` 。
    -   システム変数を読み取るときに`Scope`のチェックを追加します[＃6958](https://github.com/pingcap/tidb/pull/6958)
    -   `MAX_EXECUTION_TIME`構文[＃7012](https://github.com/pingcap/tidb/pull/7012)をサポートします
    -   `SET`構文[＃7020](https://github.com/pingcap/tidb/pull/7020)のより多くのステートメントをサポートする
    -   システム変数を設定するときに妥当性チェックを追加する[＃7117](https://github.com/pingcap/tidb/pull/7117)
    -   `Prepare`ステートメント[＃7162](https://github.com/pingcap/tidb/pull/7162)に`PlaceHolder`の数の検証を追加します。
    -   [＃7353](https://github.com/pingcap/tidb/pull/7353) `set character_set_results = null`
    -   `flush status`構文[＃7369](https://github.com/pingcap/tidb/pull/7369)をサポートします
    -   [＃7347](https://github.com/pingcap/tidb/pull/7347)で`SET`および`ENUM`タイプの列サイズを修正し`information_schema`
    -   テーブルを作成するためのステートメントの`NATIONAL CHARACTER`構文をサポートする[＃7378](https://github.com/pingcap/tidb/pull/7378)
    -   `LOAD DATA`ステートメント[＃7391](https://github.com/pingcap/tidb/pull/7391)で`CHARACTER SET`構文をサポートします。
    -   `SET`および`ENUM`タイプの列情報を修正します[＃7417](https://github.com/pingcap/tidb/pull/7417)
    -   `CREATE USER`ステートメント[＃7402](https://github.com/pingcap/tidb/pull/7402)で`IDENTIFIED WITH`構文をサポートします。
    -   `TIMESTAMP`の計算プロセス[＃7418](https://github.com/pingcap/tidb/pull/7418)での精度低下の問題を修正
    -   より多くの`SYSTEM`変数の妥当性検証をサポートします[＃7196](https://github.com/pingcap/tidb/pull/7196)
    -   `CHAR_LENGTH`関数がバイナリ文字列[＃7410](https://github.com/pingcap/tidb/pull/7410)を計算するときの誤った結果を修正します
    -   [＃7448](https://github.com/pingcap/tidb/pull/7448)を含むステートメントの誤った`CONCAT`の結果を修正し`GROUP BY`
    -   `DECIMAL`タイプを`STRING`タイプ[＃7451](https://github.com/pingcap/tidb/pull/7451)にキャストするときの不正確なタイプの長さの問題を修正します
-   DML
    -   `Load Data`ステートメント[＃6927](https://github.com/pingcap/tidb/pull/6927)の安定性の問題を修正します
    -   いくつかの`Batch`の操作を実行するときのメモリ使用量の問題を修正します[＃7086](https://github.com/pingcap/tidb/pull/7086)
    -   `Replace Into`ステートメント[＃7027](https://github.com/pingcap/tidb/pull/7027)のパフォーマンスを向上させる
    -   `CURRENT_TIMESTAMP`を書くときの一貫性のない精度の問題を修正し[＃7355](https://github.com/pingcap/tidb/pull/7355)
-   DDL
    -   場合によっては誤判断を避けるために、 `Schema`が複製されているかどうかを判断するDDLの方法を改善します[＃7319](https://github.com/pingcap/tidb/pull/7319)
    -   インデックスプロセス[＃6993](https://github.com/pingcap/tidb/pull/6993)を追加する際の`SHOW CREATE TABLE`の結果を修正します
    -   制限なし`sql-mode`では、デフォルト`blob`の`text`を[＃7230](https://github.com/pingcap/tidb/pull/7230)にすることができ`json` 。
    -   `ADD INDEX`の問題を修正する場合があります[＃7142](https://github.com/pingcap/tidb/pull/7142)
    -   `UNIQUE-KEY`のインデックス操作を追加する速度を大幅に向上させます[＃7132](https://github.com/pingcap/tidb/pull/7132)
    -   UTF-8文字セット[＃7109](https://github.com/pingcap/tidb/pull/7109)のプレフィックスインデックスの切り捨ての問題を修正しました
    -   環境変数`tidb_ddl_reorg_priority`を追加して、 `add-index`の操作の優先度を制御します[＃7116](https://github.com/pingcap/tidb/pull/7116)
    -   `information_schema.tables`分の`AUTO-INCREMENT`の表示の問題を修正します[＃7037](https://github.com/pingcap/tidb/pull/7037)
    -   `admin show ddl jobs <number>`コマンドをサポートし、指定された数のDDLジョブの出力をサポートします[＃7028](https://github.com/pingcap/tidb/pull/7028)
    -   並列DDLジョブ実行のサポート[＃6955](https://github.com/pingcap/tidb/pull/6955)
-   [テーブルパーティション](https://github.com/pingcap/tidb/projects/6) （実験的）
    -   トップレベルのパーティションをサポートする
    -   サポート`Range Partition`

## PD {#pd}

-   特徴
    -   バージョン管理メカニズムを導入し、互換性のあるクラスタのローリング更新をサポートします
    -   `region merge`つの機能を有効にする
    -   `GetPrevRegion`のインターフェースをサポート
    -   リージョンのバッチ分割をサポート
    -   GCセーフポイントの保存をサポート
-   改善
    -   TSO割り当てがシステムクロックの逆方向に影響を受けるという問題を最適化します
    -   リージョンハートビートの処理のパフォーマンスを最適化する
    -   リージョンツリーのパフォーマンスを最適化する
    -   ホットスポット統計の計算のパフォーマンスを最適化する
    -   APIインターフェースのエラーコードを返すように最適化する
    -   スケジューリング戦略を制御するオプションを追加する
    -   `label`で特殊文字を使用することを禁止する
    -   スケジューリングシミュレーターを改善する
    -   pd-ctlの統計を使用したリージョンの分割をサポート
    -   pd-ctlで`jq`を呼び出すことにより、JSON出力のフォーマットをサポートします
    -   Raftステートマシンに関するメトリックを追加します
-   バグの修正
    -   リーダーを切り替えた後に名前空間が再ロードされない問題を修正します
    -   名前空間のスケジューリングがスケジュールの制限を超える問題を修正します
    -   ホットスポットのスケジュールがスケジュールの制限を超える問題を修正します
    -   PDクライアントを閉じたときに間違ったログが出力される問題を修正します
    -   リージョンハートビートレイテンシの誤った統計を修正

## TiKV {#tikv}

-   特徴
    -   ホットリージョンでの書き込み操作によって引き起こされる大きすぎるリージョンを回避するために`batch split`をサポートします
    -   行数に基づくリージョンの分割をサポートして、インデックススキャンの効率を向上させます
-   パフォーマンス
    -   `LocalReader`を使用して、読み取り操作をraftstoreスレッドから分離し、読み取り待ち時間を短縮します
    -   MVCCフレームワークをリファクタリングし、メモリ使用量を最適化し、スキャン読み取りパフォーマンスを向上させます
    -   統計推定に基づくリージョンの分割をサポートして、I/Oの使用量を削減します
    -   ロールバックレコードに対する継続的な書き込み操作によって読み取りパフォーマンスが影響を受けるという問題を最適化します
    -   プッシュダウンアグリゲーションコンピューティングのメモリ使用量を削減します
-   改善
    -   多数の組み込み関数のプッシュダウンサポートとより優れた文字セットサポートを追加します
    -   GCワークフローを最適化し、GC速度を向上させ、システムへのGCの影響を減らします
    -   ネットワークが異常な場合にサービスリカバリを高速化するには、 `prevote`を有効にします
    -   RocksDBログファイルの関連する構成アイテムを追加します
    -   `scheduler_latch`のデフォルト構成を調整します
    -   tikv-ctlを使用してデータを手動で圧縮するときにRocksDBの最下層のデータを圧縮するかどうかの設定をサポート
    -   TiKVを開始するときに環境変数のチェックを追加します
    -   既存のデータに基づいて`dynamic_level_bytes`つのパラメーターを動的に構成することをサポートします
    -   ログ形式のカスタマイズをサポート
    -   tikv-ctlにtikv-failを統合する
    -   スレッドのI/Oメトリックを追加する
-   バグの修正
    -   10進数関連の問題を修正
    -   `gRPC max_send_message_len`が誤って設定される問題を修正します
    -   `region_size`の設定ミスによって引き起こされる問題を修正します