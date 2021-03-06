---
title: TiCDC Glossary
summary: Learn the terms about TiCDC and their definitions.
---

# TiCDC用語集 {#ticdc-glossary}

この用語集は、TiCDC関連の用語と定義を提供します。これらの用語は、TiCDCログ、監視メトリック、構成、およびドキュメントに表示されます。

TiDB関連の用語と定義については、 [TiDB用語集](/glossary.md)を参照してください。

## C {#c}

### 捕獲 {#capture}

クラスタのレプリケーションタスクが実行される単一のTiCDCインスタンス。複数のキャプチャがTiCDCクラスタを形成します。

### 変更されたデータ {#changed-data}

DMLによるデータの変更やDDLによるテーブルスキーマの変更など、アップストリームTiDBクラスタからTiCDCに書き込まれるデータ。

### チェンジフィード {#changefeed}

TiCDCのインクリメンタルレプリケーションタスク。TiDBクラスタの複数のテーブルのデータ変更ログを指定されたダウンストリームに出力します。

## O {#o}

### オーナー {#owner}

TiCDCクラスタを管理し、クラスタのレプリケーションタスクをスケジュールする特別な役割の[捕獲](#capture) 。所有者はキャプチャによって選出され、いつでも最大で1人の所有者がいます。

## P {#p}

### プロセッサー {#processor}

TiCDCレプリケーションタスクはTiCDCインスタンスにデータテーブルを割り当て、プロセッサはこれらのテーブルのレプリケーション処理ユニットを参照します。プロセッサのタスクには、変更されたデータのプル、並べ替え、復元、および配布が含まれます。
