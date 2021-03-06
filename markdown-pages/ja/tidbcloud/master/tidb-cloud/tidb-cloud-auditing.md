---
title: Audit Logging
summary: Learn about how to audit a cluster in TiDB Cloud.
---

# 監査ログ {#audit-logging}

TiDB Cloudは、ユーザーアクセスの詳細（実行されたSQLステートメントなど）の履歴をログに記録するためのデータベース監査ログ機能を提供します。

> **ノート：**
>
> 現在、**監査ログ**機能は実験的です。インターフェイスと出力は変更される可能性があります。

組織のユーザーアクセスポリシーやその他の情報セキュリティ対策の有効性を評価するには、データベース監査ログの定期的な分析を実施することがセキュリティのベストプラクティスです。

監査ログ機能はデフォルトで無効になっています。クラスタを監査するには、最初に監査ログを有効にしてから、監査フィルタールールを指定する必要があります。

> **ノート：**
>
> 監査ログはクラスタリソースを消費するため、クラスタを監査するかどうかについて慎重に検討してください。

## 前提条件 {#prerequisites}

-   TiDB Cloud専用層またはPOC層を使用しています。監査ログは、 TiDB Cloud開発者層クラスターでは使用できません。
-   あなたはTiDB Cloudの組織の監査管理者です。そうしないと、 TiDB Cloudコンソールに監査関連のオプションが表示されません。詳細については、 [メンバーの役割を構成する](/tidb-cloud/manage-user-access.md#configure-member-roles)を参照してください。

## AWSまたはGCPの監査ログを有効にする {#enable-audit-logging-for-aws-or-gcp}

TiDB Cloudが監査ログをクラウドバケットに書き込めるようにするには、最初に監査ログを有効にする必要があります。

<SimpleTab>
<div label="AWS">

### AWSの監査ログを有効にする {#enable-audit-logging-for-aws}

AWSの監査ログを有効にするには、次の手順を実行します。

#### ステップ1.AmazonS3バケットを作成します {#step-1-create-an-amazon-s3-bucket}

TiDB Cloudバケットを指定します。

詳細については、AWSユーザーガイドの[バケットの作成](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html)を参照してください。

#### ステップ2.AmazonS3アクセスを設定します {#step-2-configure-amazon-s3-access}

> **ノート：**
>
> プロジェクト内の1つのクラスタに対してAmazonS3アクセス設定が実行されると、同じプロジェクト内のすべてのクラスターからの監査ログの宛先として同じバケットを使用できます。

1.  監査ログを有効にするTiDB CloudアカウントIDとTiDBクラスタの外部IDを取得します。

    1.  TiDB Cloudコンソールで、AWSにデプロイされたプロジェクトとクラスタを選択します。
    2.  [**設定]** &gt;[<strong>監査設定]</strong>を選択します。 [<strong>ログの監査</strong>]ダイアログボックスが表示されます。
    3.  [ **Audit Logging** ]ダイアログボックスで、[ <strong>ShowAWSIAMポリシー設定</strong>]をクリックします。対応するTiDB CloudアカウントIDとTiDBクラスタのTiDB Cloud外部IDが表示されます。
    4.  後で使用するために、 TiDB CloudアカウントIDと外部IDを記録します。

2.  AWSマネジメントコンソールで、[ **IAM]** &gt; [<strong>アクセス管理</strong>]&gt;[<strong>ポリシー</strong>]に移動し、 `s3:PutObject`の書き込み専用権限を持つストレージバケットポリシーが存在するかどうかを確認します。

    -   はいの場合、後で使用するために、一致したストレージバケットポリシーを記録します。
    -   そうでない場合は、[ **IAM]** &gt;[<strong>アクセス管理</strong>]&gt;[<strong>ポリシー]</strong> &gt;[ポリシーの<strong>作成]</strong>に移動し、次のポリシーテンプレートに従ってバケットポリシーを定義します。

        
        ```
        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": "s3:PutObject",
                    "Resource": "<Your S3 bucket ARN>/*"
                }
            ]
        }
        ```

        テンプレートでは、 `<Your S3 bucket ARN>`は監査ログファイルが書き込まれるS3バケットのAmazonリソース名（ARN）です。 S3バケットの[**プロパティ**]タブに移動し、[<strong>バケットの概要</strong>]領域でAmazonリソース名（ARN）の値を取得できます。 `"Resource"`フィールドでは、ARNの後に`/*`を追加する必要があります。たとえば、ARNが`arn:aws:s3:::tidb-cloud-test`の場合、 `"Resource"`フィールドの値を`"arn:aws:s3:::tidb-cloud-test/*"`として構成する必要があります。

3.  [ **IAM** ]&gt;[<strong>アクセス管理</strong>]&gt;[<strong>ロール</strong>]に移動し、信頼エンティティがTiDB CloudアカウントIDと以前に記録した外部IDに対応するロールが既に存在するかどうかを確認します。

    -   はいの場合、後で使用するために一致した役割を記録します。
    -   そうでない場合は、[**ロールの作成**]をクリックし、信頼エンティティタイプとして[<strong>別のAWSアカウント</strong>]を選択してから、[<strong>アカウントID]</strong>フィールドにTiDB CloudアカウントIDの値を入力します。次に、[<strong>外部IDが必要</strong>]オプションを選択し、[外部ID]フィールドにTiDB Cloudの<strong>外部</strong>ID値を入力します。

4.  [ **IAM** ]&gt;[<strong>アクセス管理</strong>]&gt;[<strong>ロール</strong>]で、前の手順のロール名をクリックして<strong>[概要</strong>]ページに移動し、次の手順を実行します。

    1.  [**権限**]タブで、 `s3:PutObject`の書き込み専用権限を持つ記録されたポリシーがロールに添付されているかどうかを確認します。そうでない場合は、[ポリシーの添付]を選択し、必要な<strong>ポリシー</strong>を検索して、[<strong>ポリシーの添付</strong>]をクリックします。
    2.  **[概要**]ページに戻り、<strong>ロールARN</strong>値をクリップボードにコピーします。

#### 手順3.監査ログを有効にする {#step-3-enable-audit-logging}

TiDB Cloudコンソールで、 **TiDBCloud**アカウントIDと外部ID値を取得した[TiDB Cloud ]ダイアログボックスに戻り、次の手順を実行します。

1.  [**バケットURL]**フィールドに、監査ログファイルが書き込まれるS3バケットのURLを入力します。

2.  [**バケットリージョン]**ドロップダウンリストで、バケットが配置されているAWSリージョンを選択します。

3.  [**役割ARN]**フィールドに、 [ステップ2.AmazonS3アクセスを設定します](#step-2-configure-amazon-s3-access)でコピーした役割ARN値を入力します。

4.  [**接続のテスト**]をクリックして、 TiDB Cloudがバケットにアクセスして書き込むことができるかどうかを確認します。

    成功すると、**パス**が表示されます。それ以外の場合は、アクセス構成を確認してください。

5.  右上隅で、監査設定を**オン**に切り替えます。

    TiDB Cloudは、指定されたクラスタの監査ログをAmazonS3バケットに書き込む準備ができています。

> **ノート：**
>
> -   監査ログを有効にした後、バケットのURL、場所、またはARNに新しい変更を加えた場合は、[**再起動**]をクリックして変更をロードし、[<strong>接続のテスト</strong>]チェックを再実行して変更を有効にする必要があります。
> -   TiDB CloudからAmazonS3アクセスを削除するには、追加した信頼ポリシーを削除するだけです。

</div>

<div label="GCP">

### GCPの監査ログを有効にする {#enable-audit-logging-for-gcp}

GCPの監査ログを有効にするには、次の手順を実行します。

#### 手順1.GCSバケットを作成する {#step-1-create-a-gcs-bucket}

TiDB Cloudが監査ログを書き込む宛先として、企業所有のGCPアカウントでGoogle Cloud Storage（GCS）バケットを指定します。

詳細については、GoogleCloudStorageのドキュメントの[ストレージバケットの作成](https://cloud.google.com/storage/docs/creating-buckets)をご覧ください。

#### ステップ2.GCSアクセスを構成する {#step-2-configure-gcs-access}

> **ノート：**
>
> プロジェクト内の1つのクラスタに対してGCSアクセス構成が実行されると、同じプロジェクト内のすべてのクラスターからの監査ログの宛先として同じバケットを使用できます。

1.  監査ログを有効にするTiDBクラスタのGoogleCloudServiceアカウントIDを取得します。

    1.  TiDB Cloudコンソールで、GoogleCloudPlatformにデプロイされたプロジェクトとクラスタを選択します。
    2.  [**設定]** &gt;[<strong>監査設定]</strong>を選択します。 [<strong>ログの監査</strong>]ダイアログボックスが表示されます。
    3.  [ **Google CloudサービスアカウントIDを表示]**をクリックし、後で使用できるようにサービスアカウントIDをコピーします。

2.  Google Cloud Platform（GCP）管理コンソールで、[ **IAMと管理**]&gt; [<strong>ロール</strong>]に移動し、ストレージコンテナの次の書き込み専用権限を持つロールが存在するかどうかを確認します。

    -   storage.objects.create
    -   storage.objects.delete

    はいの場合、後で使用するためにTiDBクラスタの一致した役割を記録します。そうでない場合は、[ **IAMと管理**]&gt;[<strong>ロール</strong>]&gt;[ <strong>CREATE ROLE</strong> ]に移動して、TiDBクラスタのロールを定義します。

3.  **Cloud Storage** &gt; <strong>Browser</strong>に移動し、 TiDB CloudがアクセスするGCSバケットを選択して、 <strong>SHOWINFOPANEL</strong>をクリックします。

    パネルが表示されます。

4.  パネルで、[ **PRINCIPAL**の追加]をクリックします。

    プリンシパルを追加するためのダイアログボックスが表示されます。

5.  ダイアログボックスで、次の手順を実行します。

    1.  [**新しいプリンシパル**]フィールドに、TiDBクラスタのGoogleCloudServiceアカウントIDを貼り付けます。
    2.  [**役割**]ドロップダウンリストで、ターゲットTiDBクラスタの役割を選択します。
    3.  [**保存]**をクリックします。

#### 手順3.監査ログを有効にする {#step-3-enable-audit-logging}

TiDB Cloudコンソールで、 **TiDBCloud**アカウントIDを取得した[TiDB Cloud ]ダイアログボックスに戻り、次の手順を実行します。

1.  [**バケットURL]**フィールドに、完全なGCSバケット名を入力します。

2.  [**バケット領域**]フィールドで、バケットが配置されているGCS領域を選択します。

3.  [**接続のテスト**]をクリックして、 TiDB Cloudがバケットにアクセスして書き込むことができるかどうかを確認します。

    成功すると、**パス**が表示されます。それ以外の場合は、アクセス構成を確認してください。

4.  右上隅で、監査設定を**オン**に切り替えます。

    TiDB Cloudは、指定されたクラスタの監査ログをAmazonS3バケットに書き込む準備ができています。

> **ノート：**
>
> -   監査ログを有効にした後、バケットのURLまたは場所に新しい変更を加えた場合は、[**再起動**]をクリックして変更をロードし、[<strong>接続のテスト</strong>]チェックを再実行して変更を有効にする必要があります。
> -   TiDB CloudからGCSアクセスを削除するには、追加したプリンシパルを削除するだけです。

</div>
</SimpleTab>

## 監査フィルタールールを指定する {#specify-auditing-filter-rules}

監査ログを有効にした後、監査フィルタールールを指定して、どのユーザーアクセスイベントをキャプチャして監査ログに書き込むか、どのイベントを無視するかを制御する必要があります。フィルタルールが指定されていない場合、 TiDB Cloudは何もログに記録しません。

クラスタの監査フィルタルールを指定するには、次の手順を実行します。

1.  **監査ログを有効にする[監査ログ**]ダイアログボックスで、下にスクロールして[<strong>フィルタールール</strong>]セクションを見つけます。
2.  1つ以上のフィルタールールを1行に1つずつ追加します。各ルールは、ユーザー式、データベース式、テーブル式、およびアクセスタイプを指定します。

> **ノート：**
>
> -   フィルタルールは正規表現であり、大文字と小文字が区別されます。ワイルドカードルール`.*`を使用すると、クラスタのすべてのユーザー、データベース、またはテーブルイベントがログに記録されます。
> -   監査ログはクラスタリソースを消費するため、フィルタールールを指定するときは慎重に行ってください。消費を最小限に抑えるために、可能な場合は、監査ログの範囲を特定のデータベースオブジェクト、ユーザー、およびアクションに制限するフィルタールールを指定することをお勧めします。

## 監査ログをビューする {#view-audit-logs}

TiDB Cloud監査ログは、クラスタID、ポッドID、およびログ作成日が完全修飾ファイル名に組み込まれた読み取り可能なテキストファイルです。

たとえば、 `13796619446086334065/tidb-0/tidb-audit-2022-04-21T18-16-29.529.log` 。この例では、 `13796619446086334065`はクラスタIDを示し、 `tidb-0`はポッドIDを示します。

## 監査ログを無効にする {#disable-audit-logging}

クラスタを監査する必要がなくなった場合は、クラスタのページに移動し、[**設定]** &gt; [<strong>監査設定]</strong>をクリックして、右上隅の監査設定を<strong>[オフ</strong>]に切り替えます。

## 監査ログフィールド {#audit-log-fields}

監査ログのデータベースイベントレコードごとに、TiDBは次のフィールドを提供します。

> **ノート：**
>
> 次の表で、フィールドの最大長が空であることは、このフィールドのデータ型が明確に定義された一定の長さ（たとえば、INTEGERの場合は4バイト）であることを意味します。

| 列＃ | フィールド名         | TiDBデータ型 | 最大長   | 説明                                 |
| -- | -------------- | -------- | ----- | ---------------------------------- |
| 1  | 該当なし           | 該当なし     | 該当なし  | 内部使用のために予約済み                       |
| 2  | 該当なし           | 該当なし     | 該当なし  | 内部使用のために予約済み                       |
| 3  | 該当なし           | 該当なし     | 該当なし  | 内部使用のために予約済み                       |
| 4  | ID             | 整数       |       | 一意のイベントID                          |
| 5  | タイムスタンプ        | タイムスタンプ  |       | イベントの時間                            |
| 6  | EVENT_CLASS    | VARCHAR  | 15    | イベントタイプ                            |
| 7  | EVENT_SUBCLASS | VARCHAR  | 15    | イベントサブタイプ                          |
| 8  | STATUS_CODE    | 整数       |       | ステートメントの応答ステータス                    |
| 9  | COST_TIME      | 整数       |       | ステートメントに費やされた時間                    |
| 10 | ホスト            | VARCHAR  | 16    | サーバーIP                             |
| 11 | CLIENT_IP      | VARCHAR  | 16    | クライアントIP                           |
| 12 | ユーザー           | VARCHAR  | 17    | ログインユーザー名                          |
| 13 | データベース         | VARCHAR  | 64    | イベント関連データベース                       |
| 14 | テーブル           | VARCHAR  | 64    | イベント関連のテーブル名                       |
| 15 | SQL_TEXT       | VARCHAR  | 64 KB | マスクされたSQLステートメント                   |
| 16 | 行              | 整数       |       | 影響を受ける行の数（ `0`は、影響を受ける行がないことを示します） |

TiDBによって設定されたEVENT_CLASSフィールド値に応じて、監査ログのデータベースイベントレコードには、次のような追加のフィールドも含まれます。

-   EVENT_CLASS値が`CONNECTION`の場合、データベースイベントレコードには次のフィールドも含まれます。

    | 列＃ | フィールド名               | TiDBデータ型 | 最大長  | 説明                                                 |
    | -- | -------------------- | -------- | ---- | -------------------------------------------------- |
    | 17 | CLIENT_PORT          | 整数       |      | クライアントポート番号                                        |
    | 18 | CONNECTION_ID        | 整数       |      | 接続ID                                               |
    | 19 | 接続タイプ                | VARCHAR  | 12   | `socket`または`unix-socket`を介した接続                     |
    | 20 | SERVER_ID            | 整数       |      | TiDBサーバーID                                         |
    | 21 | サーバポート               | 整数       |      | TiDBサーバーがMySQLプロトコルを介して通信するクライアントをリッスンするために使用するポート |
    | 22 | SERVER_OS_LOGIN_USER | VARCHAR  | 17   | TiDBプロセス起動システムのユーザー名                               |
    | 23 | OS_VERSION           | VARCHAR  | 該当なし | TiDBサーバーが配置されているオペレーティングシステムのバージョン                 |
    | 24 | SSL_VERSION          | VARCHAR  | 6    | TiDBの現在のSSLバージョン                                   |
    | 25 | PID                  | 整数       |      | TiDBプロセスのPID                                       |

-   EVENT_CLASS値が`TABLE_ACCESS`または`GENERAL`の場合、データベースイベントレコードには次のフィールドも含まれます。

    | 列＃ | フィールド名        | TiDBデータ型 | 最大長 | 説明                 |
    | -- | ------------- | -------- | --- | ------------------ |
    | 17 | CONNECTION_ID | 整数       |     | 接続ID               |
    | 18 | 指図            | VARCHAR  | 14  | MySQLプロトコルのコマンドタイプ |
    | 19 | SQL_STATEMENT | VARCHAR  | 17  | SQLステートメントタイプ      |
    | 20 | PID           | 整数       |     | TiDBプロセスのPID       |
