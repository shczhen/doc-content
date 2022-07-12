---
title: Overview Page
summary: Learn the overview page of TiDB Dashboard.
---

# 概要ページ {#overview-page}

このページには、以下の情報を含む、TiDBクラスタ全体の概要が表示されます。

-   クラスタ全体の1秒あたりのクエリ数（QPS）。
-   クラスタ全体のクエリレイテンシ。
-   最近の期間で最も長い実行時間を蓄積したSQLステートメント。
-   最近の期間の実行時間がしきい値を超えている低速クエリ。
-   各インスタンスのノード数とステータス。
-   メッセージを監視および警告します。

## ページにアクセスする {#access-the-page}

TiDBダッシュボードにログインした後、デフォルトで概要ページが表示されます。または、左側のナビゲーションメニューの[**概要**]をクリックして、このページに入ることができます。

![Enter overview page](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-access.png)

## QPS {#qps}

この領域には、最近1時間のクラスタ全体の1秒あたりの成功および失敗したクエリの数が表示されます。

![QPS](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-qps.png)

> **ノート：**
>
> この機能は、Prometheusモニタリングコンポーネントがデプロイされているクラスタでのみ使用できます。 Prometheusがデプロイされていない場合、エラーが表示されます。

## レイテンシー {#latency}

この領域は、最近1時間のクラスタ全体でのクエリの99.9％、99％、および90％のレイテンシーを示しています。

![Latency](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-latency.png)

> **ノート：**
>
> この機能は、Prometheusモニタリングコンポーネントがデプロイされているクラスタでのみ使用できます。 Prometheusがデプロイされていない場合、エラーが表示されます。

## Top SQLステートメント {#top-sql-statements}

この領域には、最近の期間にクラスタ全体で最長の実行時間を累積した10種類のSQLステートメントが表示されます。クエリパラメータが異なるが構造が同じSQLステートメントは、同じSQLタイプに分類され、同じ行に表示されます。

![Top SQL](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-top-statements.png)

この領域に表示される情報は、より詳細な[SQLステートメントページ](/dashboard/dashboard-statement-list.md)と一致しています。 [**Top SQLステートメント**]見出しをクリックして、完全なリストを表示できます。この表の列の詳細については、 [SQLステートメントページ](/dashboard/dashboard-statement-list.md)を参照してください。

> **ノート：**
>
> この機能は、SQLステートメント機能が有効になっているクラスタでのみ使用できます。

## 最近の遅いクエリ {#recent-slow-queries}

デフォルトでは、この領域には、最近30分間のクラスタ全体での最新の10個の低速クエリが表示されます。

![Recent slow queries](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-slow-query.png)

デフォルトでは、300ミリ秒より長く実行されたSQLクエリは低速クエリとしてカウントされ、テーブルに表示されます。このしきい値は、 [tidb_slow_log_threshold](/system-variables.md#tidb_slow_log_threshold)変数または[遅いしきい値](/tidb-configuration-file.md#slow-threshold)パラメーターを変更することで変更できます。

この領域に表示されるコンテンツは、より詳細な[遅いクエリページ](/dashboard/dashboard-slow-query.md)と一致しています。 [**最近の低速クエリ**]タイトルをクリックして、完全なリストを表示できます。この表の列の詳細については、この[遅いクエリページ](/dashboard/dashboard-slow-query.md)を参照してください。

> **ノート：**
>
> この機能は、低速のクエリログが有効になっているクラスタでのみ使用できます。デフォルトでは、TiUPを使用してデプロイされたクラスタで低速クエリログが有効になっています。

## インスタンス {#instances}

この領域には、クラスタ全体でのTiDB、TiKV、PD、およびTiFlashのインスタンスと異常なインスタンスの総数がまとめられています。

![Instances](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-instances.png)

上の画像のステータスは次のとおりです。

-   Up：インスタンスは正常に実行されています（オフラインストレージインスタンスを含む）。
-   ダウン：インスタンスは、ネットワークの切断、プロセスのクラッシュなど、異常に実行されています。

**インスタンス**のタイトルをクリックして、各インスタンスの詳細な実行ステータスを示す[クラスター情報ページ](/dashboard/dashboard-cluster-info.md)を入力します。

## 監視と警告 {#monitor-and-alert}

この領域には、詳細なモニターとアラートを表示するためのリンクがあります。

![Monitor and alert](https://download.pingcap.com/images/docs/dashboard/dashboard-overview-monitor.png)

-   **メトリックのビュー**：このリンクをクリックして、クラスタの詳細な監視情報を表示できるGrafanaダッシュボードにジャンプします。 Grafanaダッシュボードの各モニタリング指標の詳細については、 [モニタリング指標](/grafana-overview-dashboard.md)を参照してください。
-   **アラートのビュー**：このリンクをクリックして、クラスタの詳細なアラート情報を表示できるAlertManagerページにジャンプします。アラートがクラスタに存在する場合、アラートの数はリンクテキストに直接表示されます。
-   **診断の実行**：このリンクをクリックして、より詳細な[クラスタ診断ページ](/dashboard/dashboard-diagnostics-access.md)にジャンプします。

> **ノート：**
>
> **[メトリックのビュー]**リンクは、Grafanaノードがデプロイされているクラスタでのみ使用できます。 <strong>[アラートのビュー]</strong>リンクは、AlertManagerノードがデプロイされているクラスタでのみ使用できます。
