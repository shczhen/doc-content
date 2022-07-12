---
title: PingCAP Clinic Overview
summary: Learn about the PingCAP Clinic Diagnostic Service (PingCAP Clinic), including tool components, user scenarios, and implementation principles.
---

# PingCAPクリニックの概要 {#pingcap-clinic-overview}

PingCAPクリニック診断サービス（PingCAPクリニック）は、TiUPまたはTiDB Operatorのいずれかを使用して展開されるTiDBクラスター用にPingCAPによって提供される診断サービスです。このサービスは、クラスタの問題をリモートでトラブルシューティングするのに役立ち、クラスタのステータスをローカルですばやく確認できます。 PingCAPクリニックを使用すると、TiDBクラスタのライフサイクル全体の安定した動作を保証し、潜在的な問題を予測し、問題の可能性を減らし、クラスタの問題を迅速にトラブルシューティングし、クラスタの問題を修正できます。

PingCAPクリニックは現在テクニカルプレビュー段階にあります。このサービスは、クラスタの問題を診断するために次の2つのコンポーネントを提供します。

-   クライアントの診断：

    Diag client（Diag）は、クラスタ側にデプロイされた診断ツールです。 Diagは、クラスタ診断データを収集し、診断データをClinic Serverにアップロードし、クラスタでローカルにクイックヘルスチェックを実行するために使用されます。 Diagが収集できる診断データの完全なリストについては、 [PingCAPクリニックの診断データ](/clinic/clinic-data-instruction-for-tiup.md)を参照してください。

    > **ノート：**
    >
    > Diagは、TiDBAnsibleを使用してデプロイされたクラスターからのデータの収集を一時的**にサポートしていません**。

-   クリニックサーバー：

    Clinic Serverは、クラウドにデプロイされたクラウドサービスです。 SaaSモデルで診断サービスを提供することにより、Clinic Serverはアップロードされた診断データを受信できるだけでなく、データの保存、データの表示、クラスタ診断レポートの提供を行うオンライン診断環境としても機能します。

    現在、収集した診断データは[クリニックサーバー中国](https://clinic.pingcap.com.cn)つにのみアップロードできます。アップロードされたデータは、PingCAPによってセットアップされたAWS S3 China（Beijing）リージョンサーバーに保存されます。 Clinic Server Globalには、北米のAWSS3リージョンの1つに新しいURLとデータストレージの場所がまもなく提供されます。

## ユーザーシナリオ {#user-scenarios}

-   クラスタの問題をリモートでトラブルシューティングする

    クラスタにすぐに修正できない問題がある場合は、 [TiDBコミュニティのスラックチャネル](https://tidbcommunity.slack.com/archives/CH7TTLL7P)でサポートを依頼するか、PingCAPテクニカルサポートに連絡してください。リモートアシスタンスのテクニカルサポートに連絡するときは、クラスタからさまざまな診断データを保存し、そのデータをサポートスタッフに転送する必要があります。この場合、Diagを使用してワンクリックで診断データを収集できます。 Diagを使用すると、完全な診断データをすばやく収集できるため、複雑な手動のデータ収集操作を回避できます。データを収集した後、データをClinic Server for PingCAPテクニカルサポートスタッフにアップロードして、クラスタの問題のトラブルシューティングを行うことができます。 Clinic Serverは、アップロードされた診断データ用の安全なストレージを提供し、オンライン診断をサポートします。これにより、トラブルシューティングの効率が大幅に向上します。

-   ローカルでクラスタステータスのクイックチェックを実行します

    現在クラスタが安定して稼働している場合でも、潜在的な安定性のリスクを回避するために、クラスタを定期的にチェックする必要があります。 PingCAPクリニックが提供するローカルクイックチェック機能を使用して、クラスタの潜在的な健康リスクをチェックできます。 PingCAPクリニック Technical Previewバージョンは、クラスタ構成アイテムの合理性チェックを提供して、不合理な構成を発見し、変更の提案を提供します。

## 実装の原則 {#implementation-principles}

このセクションでは、Diagがクラスタから診断データを収集する方法に関する実装原則を紹介します。

まず、Diagは、展開ツールTiUP（tiup-cluster）またはTiDB Operator （tidb-operator）からクラスタトポロジ情報を取得します。次に、Diagは、次のようなさまざまなデータ収集方法を通じて、さまざまなタイプの診断データを収集します。

-   SCPを介してサーバーファイルを転送する

    TiUPを使用して展開されたクラスターの場合、Diagは、セキュリティコピープロトコル（SCP）を介して、ターゲットコンポーネントのノードから直接ログファイルと構成ファイルを収集できます。

-   SSHを介してリモートでコマンドを実行してデータを収集する

    TiUPを使用してデプロイされたクラスターの場合、DiagはSSH（セキュリティ Shell）を介してターゲットコンポーネントシステムに接続し、コマンド（Insightなど）を実行して、カーネルログ、カーネルパラメーター、システムとハードウェアの基本情報などのシステム情報を取得できます。

-   HTTP呼び出しを介してデータを収集する

    -   Diagは、TiDBコンポーネントのHTTPインターフェイスを呼び出すことにより、TiDB、TiKV、PD、およびその他のコンポーネントのリアルタイム構成サンプリング情報とリアルタイムパフォーマンスサンプリング情報を取得できます。
    -   PrometheusのHTTPインターフェースを呼び出すことにより、Diagはアラート情報を取得してメトリックデータを監視できます。

-   SQLステートメントを介してデータベースパラメータをクエリする

    Diagは、SQLステートメントを使用して、システム変数やTiDBの他の情報を照会できます。この方法を使用するには、データを収集するときにTiDBにアクセスするためのユーザー名とパスワードを**追加で提供する**必要があります。

## 次のステップ {#next-step}

-   オンプレミス環境でPingCAPクリニックを使用する
    -   [PingCAPクリニックのクイックスタート](/clinic/quick-start-with-clinic.md)
    -   [PingCAPクリニックを使用したTiDBクラスターのトラブルシューティング](/clinic/clinic-user-guide-for-tiup.md)
    -   [PingCAPクリニックの診断データ](/clinic/clinic-data-instruction-for-tiup.md)

-   KubernetesでPingCAPクリニックを使用する
    -   [PingCAPクリニックを使用したTiDBクラスターのトラブルシューティング](https://docs.pingcap.com/tidb-in-kubernetes/stable/clinic-user-guide)
    -   [PingCAPクリニックの診断データ](https://docs.pingcap.com/tidb-in-kubernetes/stable/clinic-data-collection)
