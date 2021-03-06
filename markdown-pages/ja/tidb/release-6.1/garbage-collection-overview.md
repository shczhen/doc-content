---
title: GC Overview
summary: Learn about Garbage Collection in TiDB.
---

# GCの概要 {#gc-overview}

TiDBは、MVCCを使用してトランザクションの同時実行性を制御します。データを更新すると、元のデータはすぐには削除されませんが、バージョンを区別するためのタイムスタンプとともに、新しいデータと一緒に保持されます。ガベージコレクション（GC）の目的は、廃止されたデータをクリアすることです。

## GCプロセス {#gc-process}

各TiDBクラスタには、GCプロセスを制御するGCリーダーとして選択されたTiDBインスタンスが含まれています。

GCはTiDBで定期的に実行されます。各GCについて、TiDBは最初に「セーフポイント」と呼ばれるタイムスタンプを計算します。次に、TiDBは、セーフポイント以降のすべてのスナップショットがデータの整合性を保持していることを前提として、廃止されたデータをクリアします。具体的には、各GCプロセスには次の3つのステップが含まれます。

1.  ロックを解決します。このステップでは、TiDBはすべてのリージョンのセーフポイントの前でロックをスキャンし、これらのロックをクリアします。
2.  範囲を削除します。このステップの間に、 `DROP TABLE` / `DROP INDEX`操作から生成された全範囲の廃止されたデータがすぐにクリアされます。
3.  GCを実行します。このステップでは、各TiKVノードがそのデータをスキャンし、各キーの不要な古いバージョンを削除します。

デフォルト設定では、GCは10分ごとにトリガーされます。各GCは、最近の10分間のデータを保持します。これは、GCの寿命がデフォルトで10分であることを意味します（セーフポイント=現在の時刻-GCの寿命）。 1ラウンドのGCの実行時間が長すぎると、このラウンドのGCが完了する前に、次のGCをトリガーする時間であっても、次のラウンドのGCは開始されません。さらに、GCの有効期間を超えた後に長期間のトランザクションが適切に実行されるためには、安全なポイントが進行中のトランザクションの開始時間（start_ts）を超えないようにします。

## 実装の詳細 {#implementation-details}

### ロックを解決する {#resolve-locks}

TiDBトランザクションモデルは[Googleのパーコレーター](https://ai.google/research/pubs/pub36726)に基づいて実装されます。これは主に、いくつかの実用的な最適化を備えた2フェーズコミットプロトコルです。最初のフェーズが終了すると、関連するすべてのキーがロックされます。これらのロックのうち、1つはプライマリロックで、もう1つはプライマリロックへのポインタを含むセカンダリロックです。 2番目のフェーズでは、プライマリロックのあるキーが書き込みレコードを取得し、そのロックが削除されます。書き込みレコードは、このキーの履歴またはトランザクションロールバックレコード内の書き込みまたは削除操作を示します。プライマリロックを置き換える書き込みレコードのタイプは、対応するトランザクションが正常にコミットされたかどうかを示します。次に、すべてのセカンダリロックが連続して交換されます。障害などの理由でこれらのセカンダリロックが保持され、置き換えられない場合でも、セカンダリロックの情報に基づいて主キーを検索し、主キーがコミットされているかどうかに基づいてトランザクション全体がコミットされているかどうかを判断できます。ただし、プライマリキー情報がGCによってクリアされ、このトランザクションにコミットされていないセカンダリロックがある場合、これらのロックをコミットできるかどうかを知ることはできません。その結果、データの整合性は保証されません。

[ロックの解決]ステップは、セーフポイントの前にロックをクリアします。これは、ロックの主キーがコミットされている場合、このロックをコミットする必要があることを意味します。それ以外の場合は、ロールバックする必要があります。主キーがまだロックされている（コミットまたはロールバックされていない）場合、このトランザクションはタイムアウトしてロールバックされたと見なされます。

ロックの解決ステップは、システム変数[`tidb_gc_scan_lock_mode`](/system-variables.md#tidb_gc_scan_lock_mode-new-in-v50)を使用して構成できる次の2つの方法のいずれかで実装されます。

> **警告：**
>
> 現在、 `PHYSICAL` （Green GC）は実験的機能です。実稼働環境で使用することはお勧めしません。

-   `LEGACY` （デフォルト）：GCリーダーは、廃止されたロックをスキャンする要求をすべてのリージョンに送信し、スキャンされたロックの主キーステータスを確認し、対応するトランザクションをコミットまたはロールバックする要求を送信します。
-   `PHYSICAL` ：TiDBはRaftレイヤーをバイパスし、各TiKVノードのデータを直接スキャンします。

### 範囲の削除 {#delete-ranges}

`DROP TABLE/INDEX`などの操作中に、連続するキーを持つ大量のデータが削除されます。各キーを削除し、後でそれらのキーに対してGCを実行すると、ストレージの再利用の実行効率が低下する可能性があります。このようなシナリオでは、TiDBは実際には各キーを削除しません。代わりに、削除する範囲と削除のタイムスタンプのみを記録します。次に、[範囲の削除]ステップは、タイムスタンプがセーフポイントより前にある範囲に対して物理的な高速削除を実行します。

### GCを行う {#do-gc}

Do GCステップは、すべてのキーの古いバージョンをクリアします。セーフポイントの後のすべてのタイムスタンプに一貫したスナップショットがあることを保証するために、この手順では、セーフポイントの前にコミットされたデータを削除しますが、削除でない限り、セーフポイントの前の各キーの最後の書き込みを保持します。

このステップでは、TiDBがセーフポイントをPDに送信するだけで、GCのラウンド全体が完了します。 TiKVはセーフポイントの変更を自動的に検出し、現在のノードのすべてのリージョンリーダーに対してGCを実行します。同時に、GCリーダーはGCの次のラウンドをトリガーし続けることができます。

> **ノート：**
>
> TiDB 5.0以降、DoGCステップは常に`DISTRIBUTED`モードを使用します。これは、各リージョンにGCリクエストを送信するTiDBサーバーによって実装されていた以前の`CENTRAL`モードに代わるものです。
