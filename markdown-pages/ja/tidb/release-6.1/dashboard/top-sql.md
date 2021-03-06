---
title: TiDB Dashboard Top SQL page
summary: Learn how to use Top SQL to find SQL statements with high CPU overhead.
---

# TiDBダッシュボードのTop SQLページ {#tidb-dashboard-top-sql-page}

Top SQLを使用すると、データベース内の各SQLステートメントのCPUオーバーヘッドをリアルタイムで監視および視覚的に調査できるため、データベースのパフォーマンスの問題を最適化および解決できます。Top SQLは、すべてのTiDBおよびTiKVインスタンスから、SQLステートメントによって要約されたCPU負荷データをいつでも継続的に収集して保存します。収集されたデータは最大30日間保存できます。Top SQLは、特定の期間にTiDBまたはTiKVインスタンスの高いCPU負荷に寄与しているSQLステートメントをすばやく特定するための視覚的なチャートと表を提供します。

Top SQLは次の機能を提供します。

-   グラフと表を使用して、CPUオーバーヘッドが最も高い上位5種類のSQLステートメントを視覚化します。
-   1秒あたりのクエリ数、平均待機時間、クエリプランなどの詳細な実行情報を表示します。
-   まだ実行されているものも含め、実行されているすべてのSQLステートメントを収集します。
-   特定のTiDBおよびTiKVインスタンスのデータの表示を許可します。

## 推奨されるシナリオ {#recommended-scenarios}

Top SQLは、パフォーマンスの問題を分析するのに適しています。以下は、いくつかの典型的なTop SQLシナリオです。

-   Grafanaチャートから、クラスタの個々のTiKVインスタンスのCPU使用率が非常に高いことがわかりました。どのSQLステートメントがCPUホットスポットを引き起こしているのかを知りたいので、それらを最適化し、すべての分散リソースをより有効に活用できます。
-   クラスタ全体のCPU使用率が非常に高く、クエリが遅いことがわかりました。最適化できるように、現在どのSQLステートメントが最も多くのCPUリソースを消費しているかをすばやく把握する必要があります。
-   クラスタのCPU使用率が大幅に変化したため、主な原因を知りたいと考えています。
-   クラスタで最もリソースを消費するSQLステートメントを分析し、それらを最適化してハードウェアコストを削減します。

Top SQLを使用して、誤ったデータや異常なクラッシュなどのパフォーマンス以外の問題を特定することはできません。

Top SQL機能はまだ初期段階であり、継続的に拡張されています。現在**サポートされていない**シナリオを次に示します。

-   トップ5以外のSQLステートメントのオーバーヘッドを分析します（たとえば、複数のビジネスワークロードが混在している場合）。
-   ユーザーやデータベースなどのさまざまなディメンションによる上位N個のSQLステートメントのオーバーヘッドの分析。
-   トランザクションロックの競合など、高いCPU負荷が原因ではないデータベースパフォーマンスの問題を分析します。

## ページにアクセスする {#access-the-page}

次のいずれかの方法を使用して、Top SQLページにアクセスできます。

-   TiDBダッシュボードにログインした後、左側のナビゲーションバーの[**Top SQL** ]をクリックします。

    ![Top SQL](https://download.pingcap.com/images/docs/dashboard/top-sql-access.png)

-   ブラウザで[http://127.0.0.1:2379/dashboard/#/topsql](http://127.0.0.1:2379/dashboard/#/topsql)にアクセスします。 `127.0.0.1:2379`を実際のPDインスタンスのアドレスとポートに置き換えます。

## Top SQLを有効にする {#enable-top-sql}

> **ノート：**
>
> Top SQLを使用するには、クラスタをデプロイするか、最新バージョンのTiUP（v1.9.0以降）またはTiDB Operator（v1.3.0以降）でアップグレードする必要があります。以前のバージョンのTiUPまたはTiDB Operatorを使用してクラスタをアップグレードした場合は、手順について[FAQ](/dashboard/dashboard-faq.md#a-required-component-ngmonitoring-is-not-started-error-is-shown)を参照してください。

Top SQLは、有効にするとクラスタのパフォーマンスにわずかな影響（平均で3％以内）があるため、デフォルトでは有効になっていません。次の手順でTop SQLを有効にできます。

1.  [Top SQLページ](#access-the-page)にアクセスします。
2.  [**設定を開く]**をクリックします。 <strong>[設定]</strong>領域の右側で、[<strong>機能</strong>を有効にする]をオンにします。
3.  [**保存]**をクリックします。

この機能を有効にした後、 Top SQLがデータをロードするまで最大1分待ちます。次に、CPU負荷の詳細を確認できます。

UIに加えて、TiDBシステム変数[`tidb_enable_top_sql`](/system-variables.md#tidb_enable_top_sql-new-in-v540)を設定することでTop SQL機能を有効にすることもできます。


```sql
SET GLOBAL tidb_enable_top_sql = 1;
```

## Top SQLを使用する {#use-top-sql}

以下は、 Top SQLを使用するための一般的な手順です。

1.  [Top SQLページ](#access-the-page)にアクセスします。

2.  負荷を監視する特定のTiDBまたはTiKVインスタンスを選択します。

    ![Select Instance](https://download.pingcap.com/images/docs/dashboard/top-sql-usage-select-instance.png)

    監視するTiDBまたはTiKVインスタンスがわからない場合は、任意のインスタンスを選択できます。また、クラスタのCPU負荷が極端に不均衡な場合は、最初にGrafanaチャートを使用して、監視する特定のインスタンスを判別できます。

3.  TopSQLによって提示されたチャートと表を観察しTop SQL。

    ![Chart and Table](https://download.pingcap.com/images/docs/dashboard/top-sql-usage-chart.png)

    棒グラフの棒のサイズは、その時点でSQLステートメントによって消費されたCPUリソースのサイズを表します。異なる色は、異なるタイプのSQLステートメントを区別します。ほとんどの場合、チャートの対応する時間範囲でCPUリソースのオーバーヘッドが高いSQLステートメントにのみ焦点を当てる必要があります。

4.  表のSQLステートメントをクリックして、詳細を表示します。 Call / sec（1秒あたりの平均クエリ数）やScan Indexes / sec（1秒あたりにスキャンされるインデックス行の平均数）など、そのステートメントのさまざまなプランの詳細な実行メトリックを確認できます。

    ![Details](https://download.pingcap.com/images/docs/dashboard/top-sql-details.png)

5.  これらの最初の手がかりに基づいて、 [SQLステートメント](/dashboard/dashboard-statement-list.md)ページまたは[遅いクエリ](/dashboard/dashboard-slow-query.md)ページをさらに調べて、CPU消費量が多いまたはSQLステートメントのデータスキャンが大きい原因を見つけることができます。

さらに、次のようにTop SQLを構成できます。

-   タイムピッカーで時間範囲を調整するか、チャートで時間範囲を選択して、問題をより正確かつ詳細に調べることができます。時間範囲を狭くすると、最大1秒の精度でより詳細なデータを提供できます。

    ![Change time range](https://download.pingcap.com/images/docs/dashboard/top-sql-usage-change-timerange.png)

-   グラフが古くなっている場合は、[**更新**]ボタンをクリックするか、[<strong>更新</strong>]ドロップダウンリストから[自動更新]オプションを選択できます。

    ![Refresh](https://download.pingcap.com/images/docs/dashboard/top-sql-usage-refresh.png)

## Top SQLを無効にする {#disable-top-sql}

次の手順に従って、この機能を無効にできます。

1.  [Top SQLページ](#access-the-page)にアクセスします。
2.  右上隅にある歯車のアイコンをクリックして設定画面を開き、[**機能を有効**にする]をオフにします。
3.  [**保存]**をクリックします。
4.  ポップアップダイアログボックスで、[**無効**にする]をクリックします。

UIに加えて、TiDBシステム変数[`tidb_enable_top_sql`](/system-variables.md#tidb_enable_top_sql-new-in-v540)を設定することにより、Top SQL機能を無効にすることもできます。


```sql
SET GLOBAL tidb_enable_top_sql = 0;
```

## よくある質問 {#frequently-asked-questions}

**1.Top SQLを有効にできず、UIに「必要なコンポーネントNgMonitoringが開始されていません」と表示されます**。

[TiDBダッシュボードFAQ](/dashboard/dashboard-faq.md#a-required-component-ngmonitoring-is-not-started-error-is-shown)を参照してください。

**2.Top SQLを有効にした後、パフォーマンスに影響はありますか？**

この機能は、クラスタのパフォーマンスにわずかな影響を及ぼします。ベンチマークによると、この機能を有効にした場合の平均パフォーマンスへの影響は通常3％未満です。

**3.この機能のステータスは何ですか？**

現在、一般に利用可能な（GA）機能であり、実稼働環境で使用できます。

**4.「その他のステートメント」の意味は何ですか？**

「その他のステートメント」は、上位5つ以外のすべてのステートメントの合計CPUオーバーヘッドをカウントします。この情報を使用して、全体と比較した上位5つのステートメントによってもたらされたCPUオーバーヘッドを知ることができます。

**5.Top SQLによって表示されるCPUオーバーヘッドとプロセスの実際のCPU使用率との関係は何ですか？**

それらの相関は強いですが、まったく同じものではありません。たとえば、複数のレプリカを書き込むコストは、TopSQLによって表示されるTop SQLオーバーヘッドにはカウントされません。一般に、CPU使用率が高いSQLステートメントでは、Top SQLに表示されるCPUオーバーヘッドが高くなります。

**6.Top SQLチャートのY軸の意味は何ですか？**

消費されたCPUリソースのサイズを表します。 SQLステートメントによって消費されるリソースが多いほど、値は高くなります。ほとんどの場合、特定の値の意味や単位を気にする必要はありません。

**7.Top SQLは実行中の（未完了の）SQLステートメントを収集しますか？**

はい。各時点でTop SQLチャートに表示されるバーは、その時点で実行中のすべてのSQLステートメントのCPUオーバーヘッドを示しています。
