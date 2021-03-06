---
title: TiDB Cloud Glossary
summary: Learn the terms used in TiDB Cloud.
category: glossary
aliases: ['/tidbcloud/glossary']
---

# TiDB Cloud用語集 {#tidb-cloud-glossary}

## C {#c}

### クラスタ層 {#cluster-tier}

クラスタの機能と容量を決定します。クラスタ層が異なれば、クラスタ内のTiDB、TiKV、およびTiFlashノードの数も異なります。

## M {#m}

### メンバー {#member}

組織およびこの組織のクラスターにアクセスできる、組織に招待されたユーザー。

## N {#n}

### ノード {#node}

データインスタンス（TiKV）またはコンピューティングインスタンス（TiDB）または分析インスタンス（TiFlash）のいずれかを指します。

## O {#o}

### 組織 {#organization}

TiDB Cloudアカウントを管理するために作成するエンティティ。これには、任意の数の複数のメンバーアカウントを持つ管理アカウントが含まれます。

### 組織のメンバー {#organization-members}

組織のメンバーは、組織の所有者から組織に招待されたユーザーです。組織のメンバーは、組織のメンバーを表示したり、組織内のプロジェクトに招待したりできます。

## P {#p}

### ポリシー {#policy}

特定のアクションやリソースへのアクセスなど、役割、ユーザー、または組織に適用される権限を定義するドキュメント。

### 事業 {#project}

組織が作成したプロジェクトに基づいて、人員、インスタンス、ネットワークなどのリソースをプロジェクトごとに個別に管理でき、プロジェクト間のリソースが相互に干渉することはありません。

### プロジェクトメンバー {#project-members}

プロジェクトメンバーは、組織の1つ以上のプロジェクトに参加するように招待されたユーザーです。プロジェクトメンバーは、クラスター、ネットワークアクセス、バックアップなどを管理できます。

## R {#r}

### ごみ箱 {#recycle-bin}

有効なバックアップを持つ削除されたクラスターのデータが保存される場所。バックアップされたクラスタが削除されると、クラスタの既存のバックアップファイルがごみ箱に移動されます。自動バックアップからのバックアップファイルの場合、ごみ箱はそれらを7日間保持します。手動バックアップからのバックアップファイルの場合、有効期限はありません。データの損失を避けるために、時間内にデータを新しいクラスタに復元することを忘れないでください。クラスタ**にバックアップがない**場合、削除されたクラスタはここに表示されないことに注意してください。

### 領域 {#region}

-   TiDB Cloudリージョン

    同じ地理的領域に展開された[TiKV](https://docs.pingcap.com/tidb/stable/tidb-storage)のノードのセット。 TiKVノードのセットは、そのリージョン内の少なくとも3つの異なるアベイラビリティーゾーンにデプロイされます。

-   TiDBリージョン

    TiDBのデータの基本単位。 TiKVは、Key-Valueスペースを一連の連続するキーセグメントに分割し、各セグメントはリージョンと呼ばれます。各リージョンのデフォルトのサイズ制限は96MBで、構成できます。

### レプリカ {#replica}

同じ地域または異なる地域に配置でき、同じデータを含む別のデータベース。レプリカは、多くの場合、災害復旧の目的またはパフォーマンスの向上のために使用されます。

## T {#t}

### TiDBクラスタ {#tidb-cluster}

機能する作業データベースを形成する[TiDB](https://docs.pingcap.com/tidb/stable/tidb-computing) （ [配置Driver](https://docs.pingcap.com/tidb/stable/tidb-scheduling) ）、および[TiKV](https://docs.pingcap.com/tidb/stable/tidb-storage)ノードの[TiFlash](https://docs.pingcap.com/tidb/stable/tiflash-overview) 。

### TiDBノード {#tidb-node}

トランザクションストアまたは分析ストアから返されたクエリからのデータを集約するコンピューティングノード。 TiDBノードの数を増やすと、クラスタが処理できる同時クエリの数が増えます。

### TiFlashノード {#tiflash-node}

TiKVからのデータをリアルタイムで複製し、リアルタイムの分析ワークロードをサポートする分析ストレージノード。

### TiKVノード {#tikv-node}

オンライントランザクション処理（OLTP）データを格納するストレージノード。高可用性のために3ノードの倍数（たとえば、3、6、9）にスケーリングされ、2つのノードがレプリカとして機能します。 TiKVノードの数を増やすと、合計スループットが増加します。

### トラフィックフィルター {#traffic-filter}

SQLクライアントを介してTiDB CloudクラスタにアクセスできるIPアドレスとクラスレスドメイン間ルーティング（CIDR）アドレスのリスト。トラフィックフィルターはデフォルトで空です。

## V {#v}

### 仮想プライベートクラウド {#virtual-private-cloud}

リソースにマネージドネットワークサービスを提供する、論理的に分離された仮想ネットワークパーティション。

### VPC {#vpc}

VirtualPrivateCloudの略。

### VPCピアリング {#vpc-peering}

仮想プライベートクラウド（ [VPC](#vpc) ）ネットワークを接続して、さまざまなVPCネットワークのワークロードがプライベートに通信できるようにします。

### VPCピアリング接続 {#vpc-peering-connection}

2つの仮想プライベートクラウド（VPC）間のネットワーク接続。これにより、プライベートIPアドレスを使用してそれらの間でトラフィックをルーティングし、データ転送を容易にすることができます。
