---
title: TiDB 2.0 RC3 Release Notes
---

# TiDB2.0RC3リリースノート {#tidb-2-0-rc3-release-notes}

2018年3月23日、TiDB2.0RC3がリリースされました。このリリースでは、MySQLの互換性、SQLの最適化、および安定性が大幅に改善されています。

## TiDB {#tidb}

-   一部のシナリオで`MAX/MIN`の間違った結果の問題を修正します
-   一部のシナリオで、 `Sort Merge Join`の結果が結合キーの順序で表示されない問題を修正します
-   境界条件での`uint`と`int`の比較のエラーを修正します
-   MySQLとの互換性を向上させるために、浮動小数点型の長さと精度のチェックを最適化します
-   時間タイプの解析エラーログを改善し、エラー情報を追加します
-   メモリ制御を改善し、 `IndexLookupExecutor`のメモリに関する統計を追加します
-   一部のシナリオでは、実行速度を`ADD INDEX`に最適化して、速度を大幅に向上させます。
-   `GROUP BY`のサブステートメントが空の場合は、Stream 集計演算子を使用して、速度を上げます。
-   `STRAIGHT_JOIN`を使用してオプティマイザで`Join Reorder`の最適化を閉じることをサポートします
-   DDLジョブのより詳細なステータス情報を`ADMIN SHOW DDL JOBS`で出力します
-   `ADMIN SHOW DDL JOB QUERIES`を使用して現在実行中のDDLジョブの元のステートメントのクエリをサポート
-   ディザスタリカバリに`ADMIN RECOVER INDEX`を使用したインデックスデータのリカバリをサポート
-   オンラインビジネスへの影響を減らすために、 `ADD INDEX`の操作に低い優先度を付けます
-   `SUM/AVG`などのJSONタイプのパラメーターを使用して集計関数をサポートする
-   OGGデータ複製ツールをサポートするために、構成ファイル内の`lower_case_table_names`のシステム変数の変更をサポートする
-   Navicat管理ツールとの互換性を向上させる
-   CRUD操作での暗黙的なRowIDの使用のサポート

## PD {#pd}

-   データを削除した後に空のリージョンまたは小さなリージョンをマージするためのリージョンマージのサポート
-   レプリカの復元中またはノードのオフライン化の速度を向上させるために、レプリカの追加中に保留中のピアが多数あるノードを無視します
-   多数の空のリージョンによって引き起こされる頻繁なスケジューリングの問題を修正します
-   異なるラベル内の不均衡なリソースのシナリオでリーダーバランスのスケジューリング速度を最適化する
-   異常な領域に関する統計を追加する

## TiKV {#tikv}

-   リージョンマージのサポート
-   Raftスナップショットプロセスが完了したらすぐにPDに通知して、バランシングを高速化します
-   RawDeleteRangeAPIを追加します
-   GetMetricAPIを追加します
-   RocksDB同期ファイルによって引き起こされるI/O変動を減らします
-   データを削除した後、スペース再利用メカニズムを最適化する
-   データ回復ツールの改善`tikv-ctl`
-   スナップショットが原因でノードのダウンが遅くなる問題を修正します
-   コプロセッサーでのストリーミングをサポートする
-   Readpoolをサポートし、 `raw_get/get/batch_get`を30％増やします
-   コプロセッサーの要求タイムアウトの構成をサポート
-   コプロセッサーでのストリーミング集約のサポート
-   リージョンハートビートでのキャリータイム情報
-   スナップショットファイルのスペース使用量を制限して、ディスクスペースを過度に消費しないようにします
-   長い間リーダーを選出できない地域を記録し、報告する
-   サーバー起動時のガベージクリーニングを高速化
-   圧縮イベントに従って、対応するリージョンのサイズ情報を更新します
-   リクエストのタイムアウトを回避するには、サイズを`scan lock`に制限します
-   `DeleteRange`を使用して、リージョンの削除を高速化します
-   RocksDBパラメーターのオンライン変更をサポート
