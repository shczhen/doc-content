---
title: Compatibility Catalog of TiDB Data Migration
summary: This document describes the compatibility between DM of different versions and upstream/downstream databases.
---

# TiDBデータ移行の互換性カタログ {#compatibility-catalog-of-tidb-data-migration}

DMは、さまざまなソースからTiDBクラスターへのデータの移行をサポートしています。データソースタイプに基づいて、DMには4つの互換性レベルがあります。

-   **一般提供（GA）** ：アプリケーションシナリオが検証され、GAテストに合格しました。
-   **実験的**：アプリケーションシナリオは検証済みですが、テストはすべてのシナリオを網羅しているわけではなく、限られた数のユーザーのみが関与しています。アプリケーションシナリオで問題が発生する場合があります。
-   **テストされていません**：DMは、反復中に常にMySQLと互換性があると予想されます。ただし、リソースの制約により、すべてのMySQLフォークがDMでテストされるわけではありません。したがって、*テストされていない*ソースまたはターゲットはDMと技術的に互換性がありますが、完全にはテストされていません。つまり、使用する前に互換性を確認する必要があります。
-   **互換性**がない：DMはデータソースと互換性がないことが証明されており、アプリケーションを本番環境で使用することはお勧めしません。

## データソース {#data-sources}

| 情報源                    | 互換性レベル | 備考                     |
| ---------------------- | ------ | ---------------------- |
| MySQL≤5.5              | 未検証    |                        |
| MySQL 5.6              | GA     |                        |
| MySQL 5.7              | GA     |                        |
| MySQL 8.0              | 実験的    |                        |
| MariaDB &lt;10.1.2     | 非互換    | 時間タイプのbinlogと互換性がありません |
| MariaDB 10.1.2〜10.5.10 | 実験的    |                        |
| MariaDB&gt; 10.5.10    | 非互換    | チェック手順で報告された許可エラー      |

## ターゲットデータベース {#target-databases}

> **警告：**
>
> DMv5.3.0は推奨されません。 GTIDレプリケーションを有効にしたが、DM v5.3.0でリレーログを有効にしない場合、データレプリケーションは低い確率で失敗します。

| 情報源      | 互換性レベル | DMバージョン         |
| -------- | ------ | --------------- |
| TiDB 6.0 | GA     | ≥5.3.1          |
| TiDB 5.4 | GA     | ≥5.3.1          |
| TiDB 5.3 | GA     | ≥5.3.1          |
| TiDB 5.2 | GA     | ≥2.0.7、推奨：5.4   |
| TiDB 5.1 | GA     | ≥2.0.4、推奨：5.4   |
| TiDB 5.0 | GA     | ≥2.0.4、推奨：5.4   |
| TiDB 4.x | GA     | ≥2.0.1、推奨：2.0.7 |
| TiDB 3.x | GA     | ≥2.0.1、推奨：2.0.7 |
| MySQL    | 実験的    |                 |
| MariaDB  | 実験的    |                 |
