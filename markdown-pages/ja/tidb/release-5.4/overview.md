---
title: TiDB Introduction
summary: Learn about the key features and usage scenarios of TiDB.
---

# TiDBの紹介 {#tidb-introduction}

[TiDB](https://github.com/pingcap/tidb) （/&#39;taɪdiːbi：/、「Ti」はTitaniumの略）は、Hybrid Transactional and Analytical Processing（HTAP）ワークロードをサポートするオープンソースのNewSQLデータベースです。 MySQLと互換性があり、水平方向のスケーラビリティ、強力な一貫性、および高可用性を備えています。 TiDBの目標は、OLTP（オンライントランザクション処理）、OLAP（オンライン分析処理）、およびHTAPサービスをカバーするワンストップデータベースソリューションをユーザーに提供することです。 TiDBは、高可用性と大規模データとの強力な整合性を必要とするさまざまなユースケースに適しています。

<video src="https://tidb-docs.s3.us-east-2.amazonaws.com/ENG+TiDB+Intro+2.mp4" width="600px" height="450px" controls poster="https://tidb-docs.s3.us-east-2.amazonaws.com/Thumbnail+-+ENG.png"></video>

## 主な機能 {#key-features}

-   **水平方向にスケールアウトまたは簡単にスケールイン**

    コンピューティングをストレージから分離するTiDBアーキテクチャ設計により、必要に応じて、コンピューティングまたはストレージ容量をオンラインで個別にスケールアウトまたはスケールアウトできます。スケーリングプロセスは、アプリケーションの運用および保守スタッフに対して透過的です。

-   **金融グレードの高可用性**

    データは複数のレプリカに保存されます。データレプリカは、Multi-Raftプロトコルを使用してトランザクションログを取得します。トランザクションは、データがレプリカの大部分に正常に書き込まれた場合にのみコミットできます。これにより、少数のレプリカがダウンした場合の強力な一貫性と可用性を保証できます。さまざまな耐災害性レベルの要件を満たすために、必要に応じて地理的な場所とレプリカの数を構成できます。

-   **リアルタイムHTAP**

    TiDBは、2つのストレージエンジンを提供します[TiKV](/tikv-overview.md)つは行ベースのストレージエンジンで、 [TiFlash](/tiflash/tiflash-overview.md)は列型ストレージエンジンです。 TiFlashは、Multi-Raft Learnerプロトコルを使用して、TiKVからのデータをリアルタイムで複製し、TiKV行ベースのストレージエンジンとTiFlash列型ストレージエンジン間のデータの一貫性を確保します。 TiKVとTiFlashは、HTAPリソース分離の問題を解決するために、必要に応じて異なるマシンに展開できます。

-   **クラウドネイティブ分散データベース**

    TiDBは、クラウド向けに設計された分散データベースであり、クラウドプラットフォームに柔軟なスケーラビリティ、信頼性、セキュリティを提供します。ユーザーは、変化するワークロードの要件を満たすためにTiDBを柔軟にスケーリングできます。 TiDBでは、各データに少なくとも3つのレプリカがあり、データセンター全体の停止を許容するために、異なるクラウドアベイラビリティーゾーンでスケジュールすることができます。 [TiDB Operator](https://docs.pingcap.com/tidb-in-kubernetes/stable/tidb-operator-overview)は、KubernetesでのTiDBの管理を支援し、TiDBクラスタの運用に関連するタスクを自動化します。これにより、管理対象のKubernetesを提供するクラウドにTiDBを簡単にデプロイできます。フルマネージドのTiDBサービスである[TiDB Cloud](https://pingcap.com/tidb-cloud/)は、 [クラウド内のTiDB](https://docs.pingcap.com/tidbcloud/)の全力を解き放つ最も簡単で、最も経済的で、最も回復力のある方法であり、数回クリックするだけでTiDBクラスターを展開および実行できます。

-   **MySQL5.7プロトコルおよびMySQLエコシステムと互換性があります**

    TiDBは、MySQL 5.7プロトコル、MySQLの一般的な機能、およびMySQLエコシステムと互換性があります。アプリケーションをTiDBに移行するために、多くの場合、1行のコードを変更する必要はなく、少量のコードを変更するだけで済みます。さらに、TiDBは、アプリケーションデータをTiDBに簡単に移行するのに役立つ一連の[データ移行ツール](/ecosystem-tool-user-guide.md)を提供します。

    <video src="https://tidb-docs.s3.us-east-2.amazonaws.com/ENG+TiDB+Intro+1.mp4" width="600px" height="450px" controls poster="https://tidb-docs.s3.us-east-2.amazonaws.com/Thumbnail+-+ENG.png"></video>

## ユースケース {#use-cases}

-   **データの一貫性、信頼性、可用性、スケーラビリティ、および災害耐性に対する高い要件を持つ金融業界のシナリオ**

    ご存知のとおり、金融業界には、データの一貫性、信頼性、可用性、スケーラビリティ、および災害耐性に対する高い要件があります。従来のソリューションは、同じ都市の2つのデータセンターでサービスを提供し、データディザスタリカバリを提供しますが、別の都市にある3番目のデータセンターではサービスを提供しません。このソリューションには、リソース使用率が低く、保守コストが高く、RTO（目標復旧時間）とRPO（目標復旧時点）が期待に応えられないという欠点があります。 TiDBは、複数のレプリカとMulti-Raftプロトコルを使用して、さまざまなデータセンター、ラック、およびマシンにデータをスケジュールします。一部のマシンに障害が発生した場合、システムは自動的に切り替えて、システムのRTOが30秒以下でRPOが0になるようにします。

-   **ストレージ容量、スケーラビリティ、および同時実行性に対する高い要件を伴う大量のデータと高い同時実行性のシナリオ**

    アプリケーションが急速に成長するにつれて、データは急増します。従来のスタンドアロンデータベースは、データ容量の要件を満たすことができません。解決策は、シャーディングミドルウェアまたはNewSQLデータベース（TiDBなど）を使用することであり、後者の方が費用効果が高くなります。 TiDBは、個別のコンピューティングおよびストレージアーキテクチャを採用しています。これにより、コンピューティングまたはストレージの容量を個別にスケールアウトまたはスケールインできます。コンピューティングレイヤーは最大512ノードをサポートし、各ノードは最大1,000の同時実行をサポートし、最大クラスタ容量はPB（ペタバイト）レベルです。

-   **リアルタイムHTAPシナリオ**

    5G、モノのインターネット、人工知能の急速な成長に伴い、企業が生成するデータは途方もなく増加し続け、数百TB（テラバイト）またはPBレベルにまで達します。従来のソリューションは、OLTPデータベースを使用してオンライントランザクションアプリケーションを処理し、ETL（抽出、変換、読み込み）ツールを使用して、データ分析のためにデータをOLAPデータベースに複製することです。このソリューションには、ストレージコストが高く、リアルタイムパフォーマンスが低いなど、複数の欠点があります。 TiDBは、v4.0でTiFlash列型ストレージエンジンを導入します。これは、TiKV行ベースのストレージエンジンと組み合わせて、真のHTAPデータベースとしてTiDBを構築します。少量の追加ストレージコストで、オンライントランザクション処理とリアルタイムデータ分析の両方を同じシステムで処理できるため、コストを大幅に節約できます。

-   **データ集約と二次処理のシナリオ**

    ほとんどの企業のアプリケーションデータは、さまざまなシステムに分散しています。アプリケーションが成長するにつれて、意思決定リーダーは、時間内に意思決定を行うために、会社全体のビジネスステータスを理解する必要があります。この場合、会社は散在するデータを同じシステムに集約し、二次処理を実行してT+0またはT+1レポートを生成する必要があります。従来のソリューションはETLとHadoopを使用することですが、Hadoopシステムは複雑であり、運用と保守のコストとストレージのコストが高くなります。 Hadoopと比較すると、TiDBははるかに単純です。 TiDBが提供するETLツールまたはデータ移行ツールを使用して、データをTiDBに複製できます。レポートは、SQLステートメントを使用して直接生成できます。

## も参照してください {#see-also}

-   [TiDBアーキテクチャ](/tidb-architecture.md)
-   [TiDBストレージ](/tidb-storage.md)
-   [TiDBコンピューティング](/tidb-computing.md)
-   [TiDBスケジューリング](/tidb-scheduling.md)
