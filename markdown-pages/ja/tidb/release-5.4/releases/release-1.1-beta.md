---
title: TiDB 1.1 Beta Release Notes
---

# TiDB1.1ベータリリースノート {#tidb-1-1-beta-release-notes}

2018年2月24日、TiDB1.1Betaがリリースされました。このリリースでは、MySQLの互換性、SQLの最適化、安定性、およびパフォーマンスが大幅に改善されています。

## TiDB {#tidb}

-   監視メトリックをさらに追加し、ログを改善します
-   より多くのMySQL構文と互換性があります
-   `information_schema`でのテーブル作成時間の表示をサポート
-   `MaxOneRow`演算子を含むクエリを最適化する
-   結合によって使用されるメモリをさらに削減するために、結合によって生成される中間結果セットのサイズを構成します
-   `tidb_config`セッション変数を追加して、現在のTiDB構成を出力します
-   `Union`および`Index Join`オペレーターのパニックの問題を修正します
-   一部のシナリオでの`Sort Merge Join`演算子の誤った結果の問題を修正
-   `Show Index`ステートメントに追加中のインデックスが表示される問題を修正します
-   `Drop Stats`ステートメントの失敗を修正します
-   SQLエンジンのクエリパフォーマンスを最適化して、Sysbench Select / OLTPのテスト結果を10％向上させます
-   新しい実行エンジンを使用して、オプティマイザーでのサブクエリの計算速度を向上させます。 TiDB 1.0と比較して、TiDB 1.1 Betaは、TPC-HやTPC-DSなどのテストで大幅な改善が見られます。

## PD {#pd}

-   ドロップリージョンデバッグインターフェイスを追加します
-   PDリーダーの優先順位設定をサポート
-   Raftリーダーをスケジュールしないように特定のラベルでストアを構成することをサポートします
-   各PDのヘルスステータスを列挙するためのインターフェイスを追加します
-   メトリックを追加する
-   PDリーダーとetcdリーダーをできるだけ同じノードにまとめます
-   TiKVがダウンしたときのデータ復元の優先度と速度を改善します
-   `data-dir`の構成項目の有効性チェックを拡張します
-   リージョンハートビートのパフォーマンスを最適化する
-   ホットスポットスケジューリングがラベル制約に違反する問題を修正します
-   その他の安定性の問題を修正する

## TiKV {#tikv}

-   潜在的なGCの問題を回避するために、オフセット+制限を使用してロックをトラバースします
-   GC速度を向上させるために、バッチでのロックの解決をサポートします
-   GCの同時実行性をサポートしてGCの速度を向上させる
-   RocksDBコンパクションリスナーを使用してリージョンサイズを更新し、より正確なPDスケジューリングを実現します
-   TiKVの起動を高速化するために、 `DeleteFilesInRanges`を使用して古いデータをバッチで削除します
-   保持されたファイルが多くのスペースを占有しないように、Raftスナップショットの最大サイズを構成します
-   `tikv-ctl`でより多くのリカバリ操作をサポート
-   順序付けられたフロー集約操作を最適化する
-   メトリックを改善し、バグを修正します
