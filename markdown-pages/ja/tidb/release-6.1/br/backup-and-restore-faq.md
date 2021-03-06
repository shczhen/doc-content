---
title: Backup & Restore FAQs
summary: Learn about Frequently Asked Questions (FAQs) and the solutions of BR.
---

# バックアップと復元に関するFAQ {#backup-x26-restore-faqs}

このドキュメントには、よくある質問（FAQ）とバックアップと復元（BR）に関するソリューションが記載されています。

## TiDB v5.4.0以降のバージョンでは、高負荷のクラスタでバックアップタスクを実行すると、バックアップタスクの速度が遅くなるのはなぜですか。 {#in-tidb-v5-4-0-and-later-versions-when-backup-tasks-are-performed-on-the-cluster-under-high-workload-why-does-the-speed-of-backup-tasks-become-slow}

TiDB v5.4.0以降、BRはバックアップタスクの自動調整機能を導入しています。 v5.4.0以降のバージョンのクラスターの場合、この機能はデフォルトで有効になっています。クラスタのワークロードが重い場合、この機能はバックアップタスクで使用されるリソースを制限して、オンラインクラスタへの影響を減らします。詳細については、 [BRオートチューン](/br/br-auto-tune.md)を参照してください。

TiKVは[動的に構成する](/tikv-control.md#modify-the-tikv-configuration-dynamically)自動調整機能をサポートしています。クラスタを再起動せずに、次の方法で機能を有効または無効にできます。

-   自動調整を無効にする：TiKV構成項目[`backup.enable-auto-tune`](/tikv-configuration-file.md#enable-auto-tune-new-in-v540)を`false`に設定します。
-   自動調整を有効にする： `backup.enable-auto-tune`を`true`に設定します。 v5.3.xからv5.4.0以降のバージョンにアップグレードされたクラスターの場合、自動調整機能はデフォルトで無効になっています。手動で有効にする必要があります。

`tikv-ctl`を使用して自動調整を有効または無効にするには、 [オートチューンを使用する](/br/br-auto-tune.md#use-auto-tune)を参照してください。

さらに、自動調整により、バックアップタスクで使用されるデフォルトのスレッド数が削減されます。詳細については、 `backup.num-threads` ]（/ tikv-configuration-file.md＃num-threads-1）を参照してください。したがって、Grafanaダッシュボードでは、バックアップタスクで使用される速度、CPU使用率、およびI / Oリソース使用率は、v5.4.0より前のバージョンよりも低くなります。 v5.4.0より前では、デフォルト値の`backup.num-threads`は`CPU * 0.75`でした。つまり、バックアップタスクで使用されるスレッドの数は、論理CPUコアの75％を占めています。最大値は`32`でした。 v5.4.0以降、この構成アイテムのデフォルト値は`CPU * 0.5`で、最大値は`8`です。

オフラインクラスタでバックアップタスクを実行する場合、バックアップを高速化するために、 `tikv-ctl`を使用して`backup.num-threads`の値をより大きな数に変更できます。

## エラーメッセージ<code>could not read local://...:download sst failed</code>場合、データの復元中に返されますが、どうすればよいですか？ {#what-should-i-do-if-the-error-message-code-could-not-read-local-download-sst-failed-code-is-returned-during-data-restoration}

データを復元する場合、各ノードは**すべての**バックアップファイル（SSTファイル）にアクセスできる必要があります。デフォルトでは、 `local`のストレージが使用されている場合、バックアップファイルは異なるノードに分散しているため、データを復元することはできません。したがって、各TiKVノードのバックアップファイルを他のTiKVノードにコピーする必要があります。

バックアップ中にNFSディスクをバックアップディスクとしてマウントすることをお勧めします。詳細については、 [1つのテーブルをネットワークディスクにバックアップします](/br/backup-and-restore-use-cases.md#back-up-a-single-table-to-a-network-disk-recommended-for-production-environments)を参照してください。

## バックアップ操作はクラスタにどの程度の影響を与えますか？ {#how-much-impact-does-a-backup-operation-have-on-the-cluster}

TiDB v5.4.0以降のバージョンでは、BRは、バックアップタスクで使用されるデフォルトのCPU使用率を削減するだけでなく、ワークロードが重いクラスタでバックアップタスクが使用するリソースを制限する[BRオートチューン](/br/br-auto-tune.md)つの機能を導入します。したがって、ワークロードが重いv5.4.0クラスタのバックアップタスクにデフォルト構成を使用する場合、クラスタのパフォーマンスに対するタスクの影響は、v5.4.0より前のクラスターの場合よりも大幅に小さくなります。

以下は、単一ノードでの内部テストです。テスト結果は、**フルスピードバックアップ**シナリオでv5.4.0とv5.3.0のデフォルト構成を使用する場合、クラスタパフォーマンスに対するBRを使用したバックアップの影響がまったく異なることを示しています。詳細なテスト結果は次のとおりです。

-   BRがv5.3.0のデフォルト構成を使用する場合、書き込み専用ワークロードのQPSは75％削減されます。
-   BRがv5.4.0のデフォルト構成を使用する場合、同じワークロードのQPSは25％削減されます。ただし、この構成を使用すると、BRを使用したバックアップタスクの期間がそれに応じて長くなります。必要な時間は、v5.3.0構成の1.7倍です。

次のいずれかのソリューションを使用して、バックアップタスクがクラスタのパフォーマンスに与える影響を手動で制御できます。これらの方法は、クラスタへのバックアップタスクの影響を軽減しますが、バックアップタスクの速度も低下させることに注意してください。

-   `--ratelimit`パラメータを使用して、バックアップタスクの速度を制限します。このパラメータは、**バックアップファイルを外部ストレージに保存**する速度を制限することに注意してください。バックアップファイルの合計サイズを計算するときは、バックアップログの`backup data size(after compressed)`をベンチマークとして使用します。
-   TiKV構成項目[`backup.num-threads`](/tikv-configuration-file.md#num-threads-1)を調整して、バックアップタスクで使用されるスレッドの数を制限します。 BRがバックアップタスクに使用するスレッドが`8`以下であり、クラスタの合計CPU使用率が60％を超えない場合、読み取りおよび書き込みのワークロードに関係なく、バックアップタスクはクラスタにほとんど影響を与えません。

## BRはシステムテーブルをバックアップしますか？データの復元中に、競合が発生しますか？ {#does-br-back-up-system-tables-during-data-restoration-do-they-raise-conflicts}

v5.1.0より前では、BRはバックアップ中にシステムスキーマ`mysql.*`からデータを除外していました。 v5.1.0以降、BRは、システムスキーマ`mysql.*`を含むすべてのデータをデフォルトで**バックアップ**します。

`mysql.*`でシステムテーブルを復元する技術的な実装はまだ完了していないため、システムスキーマ`mysql`のテーブルはデフォルトで**は復元されません**。つまり、競合は発生しません。詳細については、 [`mysql`スキーマで作成されたテーブルを復元します（実験的）](/br/br-usage-restore.md#restore-tables-created-in-the-mysql-schema)を参照してください。

## ルートを使用してBRを実行しようとしても、 <code>Permission denied</code>た、または<code>No such file or directory</code>エラーが発生しないようにするにはどうすればよいですか？ {#what-should-i-do-to-handle-the-code-permission-denied-code-or-code-no-such-file-or-directory-code-error-even-if-i-have-tried-to-run-br-using-root-in-vain}

TiKVがバックアップディレクトリにアクセスできるかどうかを確認する必要があります。データをバックアップするには、TiKVに書き込み権限があるかどうかを確認してください。データを復元するには、読み取り権限があるかどうかを確認してください。

バックアップ操作中に、記憶媒体がローカルディスクまたはネットワークファイルシステム（NFS）である場合、BRを開始するユーザーとTiKVを開始するユーザーが一致していることを確認します（BRとTiKVが異なるマシン上にある場合、ユーザー&#39;UIDは一貫している必要があります）。そうしないと、 `Permission denied`の問題が発生する可能性があります。

バックアップファイル（SSTファイル）はTiKVによって保存されるため、rootアクセスでBRを実行すると、ディスクのアクセス許可が原因で失敗する可能性があります。

> **ノート：**
>
> データの復元中にも同じ問題が発生する可能性があります。 SSTファイルを初めて読み取るときに、読み取り権限が検証されます。 DDLの実行期間は、権限の確認とBRの実行の間に長い間隔がある可能性があることを示しています。長時間待つとエラーメッセージ`Permission denied`が表示される場合があります。
>
> したがって、次の手順に従って、データを復元する前に権限を確認することをお勧めします。

1.  プロセスクエリに対してLinuxネイティブコマンドを実行します。

    
    ```bash
    ps aux | grep tikv-server
    ```

    上記のコマンドの出力：

    ```shell
    tidb_ouo  9235 10.9  3.8 2019248 622776 ?      Ssl  08:28   1:12 bin/tikv-server --addr 0.0.0.0:20162 --advertise-addr 172.16.6.118:20162 --status-addr 0.0.0.0:20188 --advertise-status-addr 172.16.6.118:20188 --pd 172.16.6.118:2379 --data-dir /home/user1/tidb-data/tikv-20162 --config conf/tikv.toml --log-file /home/user1/tidb-deploy/tikv-20162/log/tikv.log
    tidb_ouo  9236  9.8  3.8 2048940 631136 ?      Ssl  08:28   1:05 bin/tikv-server --addr 0.0.0.0:20161 --advertise-addr 172.16.6.118:20161 --status-addr 0.0.0.0:20189 --advertise-status-addr 172.16.6.118:20189 --pd 172.16.6.118:2379 --data-dir /home/user1/tidb-data/tikv-20161 --config conf/tikv.toml --log-file /home/user1/tidb-deploy/tikv-20161/log/tikv.log
    ```

    または、次のコマンドを実行できます。

    
    ```bash
    ps aux | grep tikv-server | awk '{print $1}'
    ```

    上記のコマンドの出力：

    ```shell
    tidb_ouo
    tidb_ouo
    ```

2.  TiUPコマンドを使用して、クラスタのスタートアップ情報を照会します。

    
    ```bash
    tiup cluster list
    ```

    上記のコマンドの出力：

    ```shell
    [root@Copy-of-VM-EE-CentOS76-v1 br]# tiup cluster list
    Starting component `cluster`: /root/.tiup/components/cluster/v1.5.2/tiup-cluster list
    Name          User      Version  Path                                               PrivateKey
    ----          ----      -------  ----                                               ----------
    tidb_cluster  tidb_ouo  v5.0.2   /root/.tiup/storage/cluster/clusters/tidb_cluster  /root/.tiup/storage/cluster/clusters/tidb_cluster/ssh/id_rsa
    ```

3.  バックアップディレクトリの権限を確認してください。たとえば、 `backup`はバックアップデータストレージ用です。

    
    ```bash
    ls -al backup
    ```

    上記のコマンドの出力：

    ```shell
    [root@Copy-of-VM-EE-CentOS76-v1 user1]# ls -al backup
    total 0
    drwxr-xr-x  2 root root   6 Jun 28 17:48 .
    drwxr-xr-x 11 root root 310 Jul  4 10:35 ..
    ```

    上記の出力から、 `tikv-server`つのインスタンスがユーザー`tidb_ouo`によって開始されていることがわかります。ただし、ユーザー`tidb_ouo`には`backup`の書き込み権限がありません。したがって、バックアップは失敗します。

## <code>Io(Os...)</code>エラーを処理するにはどうすればよいですか？ {#what-should-i-do-to-handle-the-code-io-os-code-error}

これらの問題のほとんどすべては、TiKVがディスクにデータを書き込むときに発生するシステムコールエラーです（たとえば、 `Io(Os {code: 13, kind: PermissionDenied...})`または`Io(Os {code: 2, kind: NotFound...})` ）。

このような問題に対処するには、まずバックアップディレクトリのマウント方法とファイルシステムを確認してから、別のフォルダまたは別のハードディスクにデータをバックアップしてみてください。

たとえば、 `samba`で構築されたネットワークディスクにデータをバックアップするときに`Code: 22(invalid argument)`エラーが発生する場合があります。

## <code>rpc error: code = Unavailable desc =...</code> BRでエラーが発生しましたか？ {#what-should-i-do-to-handle-the-code-rpc-error-code-unavailable-desc-code-error-occurred-in-br}

このエラーは、（BRを使用して）復元するクラスタの容量が不十分な場合に発生する可能性があります。このクラスタの監視メトリックまたはTiKVログを確認することで、原因をさらに確認できます。

この問題を処理するには、クラスタリソースをスケールアウトし、復元中の同時実行性を減らし、 `RATE_LIMIT`オプションを有効にすることができます。

## <code>local</code>ストレージを使用すると、バックアップされたファイルはどこに保存されますか？ {#where-are-the-backed-up-files-stored-when-i-use-code-local-code-storage}

`local`のストレージを使用すると、BRが実行されているノードで`backupmeta`が生成され、各リージョンのリーダーノードでバックアップファイルが生成されます。

## バックアップデータのサイズはどうですか？バックアップのレプリカはありますか？ {#how-about-the-size-of-the-backup-data-are-there-replicas-of-the-backup}

データのバックアップ中に、バックアップファイルが各リージョンのリーダーノードに生成されます。バックアップのサイズはデータサイズと同じであり、冗長レプリカはありません。したがって、合計データサイズは、おおよそTiKVデータの合計数をレプリカの数で割ったものになります。

ただし、ローカルストレージからデータを復元する場合は、各TiKVがすべてのバックアップファイルにアクセスできる必要があるため、レプリカの数はTiKVノードの数と同じです。

## BRがTiCDC/ Drainerのアップストリームクラスタにデータを復元する場合はどうすればよいですか？ {#what-should-i-do-when-br-restores-data-to-the-upstream-cluster-of-ticdc-drainer}

-   **BRを使用して復元されたデータは、ダウンストリームに複製できません**。これは、BRがSSTファイルを直接インポートしますが、現在、ダウンストリームクラスタがこれらのファイルをアップストリームから取得できないためです。

-   v4.0.3より前では、BRの復元中に生成されたDDLジョブにより、TiCDC/ Drainerで予期しないDDLが実行される可能性がありました。したがって、TiCDC / Drainerのアップストリームクラスタで復元を実行する必要がある場合は、BRを使用して復元されたすべてのテーブルをDrainer / Drainerブロックリストに追加します。

[`filter.rules`](https://github.com/pingcap/tiflow/blob/7c3c2336f98153326912f3cf6ea2fbb7bcc4a20c/cmd/changefeed.toml#L16)を使用してTiCDCのブロックリストを構成し、 [`syncer.ignore-table`](/tidb-binlog/tidb-binlog-configuration-file.md#ignore-table)を使用してDrainerのブロックリストを構成できます。

## BRは、テーブルの<code>SHARD_ROW_ID_BITS</code>および<code>PRE_SPLIT_REGIONS</code>情報をバックアップしますか？復元されたテーブルには複数のリージョンがありますか？ {#does-br-back-up-the-code-shard-row-id-bits-code-and-code-pre-split-regions-code-information-of-a-table-does-the-restored-table-have-multiple-regions}

はい。 BRは、テーブルの[`SHARD_ROW_ID_BITS`および<code>PRE_SPLIT_REGIONS</code>](/sql-statements/sql-statement-split-region.md#pre_split_regions)の情報をバックアップします。復元されたテーブルのデータも複数のリージョンに分割されます。

## <code>the entry too large, the max entry size is 6291456, the size of data is 7690800</code>というエラーメッセージが表示されて復元が失敗した場合は、どうすればよいですか？ {#what-should-i-do-if-the-restore-fails-with-the-error-message-code-the-entry-too-large-the-max-entry-size-is-6291456-the-size-of-data-is-7690800-code}

`--ddl-batch-size`から`128`以下の値を設定することにより、バッチで作成されるテーブルの数を減らすことができます。

BRを使用して[ `--ddl-batch-size` ]（/br/br-batch-create-table.md#how to use）の値が`1`より大きいバックアップデータを復元する場合、TiDBはテーブル作成のDDLジョブをDDLジョブキューに書き込みますそれはTiKVによって維持されています。現時点では、ジョブメッセージの最大値はデフォルトで`6 MB`であるため、TiDBによって一度に送信されるすべてのテーブルスキーマの合計サイズは6 MBを超えないようにする必要があります（この値を変更することは**お勧め**しません。詳細については、 [`txn-entry-size-limit`](/tidb-configuration-file.md#txn-entry-size-limit-new-in-v50)および[`raft-entry-max-size`](/tikv-configuration-file.md#raft-entry-max-size) ）。したがって、 `--ddl-batch-size`を過度に大きな値に設定すると、TiDBによって一度にバッチで送信されるテーブルのスキーマサイズが指定された値を超え、BRが`entry too large, the max entry size is 6291456, the size of data is 7690800`エラーを報告します。

## BRを使用してバックアップデータを復元した後、SQLクエリで<code>region is unavailable</code>というエラーが報告されるのはなぜですか？ {#why-is-the-code-region-is-unavailable-code-error-reported-for-a-sql-query-after-i-use-br-to-restore-the-backup-data}

BRを使用してバックアップされたクラスタにTiFlashがある場合、BRがバックアップデータを復元するときに`TableInfo`はTiFlash情報を保存します。復元するクラスタにTiFlashがない場合は、 `region is unavailable`エラーが報告されます。

## BRは、一部の履歴バックアップのインプレース完全復元をサポートしていますか？ {#does-br-support-in-place-full-restoration-of-some-historical-backup}

いいえ。BRは、一部の履歴バックアップのインプレース完全復元をサポートしていません。

## Kubernetes環境での増分バックアップにBRを使用するにはどうすればよいですか？ {#how-can-i-use-br-for-incremental-backup-in-the-kubernetes-environment}

最後のBRバックアップの`commitTs`フィールドを取得するには、kubectlを使用して`kubectl -n ${namespace} get bk ${name}`コマンドを実行します。このフィールドの内容は`--lastbackupts`として使用できます。

## BR backupTSをUnix時間に変換するにはどうすればよいですか？ {#how-can-i-convert-br-backupts-to-unix-time}

BR `backupTS`は、デフォルトで、バックアップが開始される前にPDから取得された最新のタイムスタンプになります。 `pd-ctl tso timestamp`を使用してタイムスタンプを解析して正確な値を取得するか、 `backupTS >> 18`を使用して推定値をすばやく取得できます。

## BRがバックアップデータを復元した後、テーブルとインデックスのTiDBの統計を更新するために、テーブルで<code>ANALYZE</code>ステートメントを実行する必要がありますか？ {#after-br-restores-the-backup-data-do-i-need-to-execute-the-code-analyze-code-statement-on-the-table-to-update-the-statistics-of-tidb-on-the-tables-and-indexes}

BRは統計をバックアップしません（v4.0.9を除く）。したがって、バックアップデータを復元した後、手動で`ANALYZE TABLE`を実行するか、TiDBが自動的に`ANALYZE`を実行するのを待つ必要があります。

v4.0.9では、BRはデフォルトで統計をバックアップするため、メモリを大量に消費します。バックアッププロセスを確実に実行するために、v4.0.10以降、統計のバックアップはデフォルトで無効になっています。

テーブルで`ANALYZE`を実行しない場合、統計が不正確であるため、TiDBは最適化された実行プランを選択できません。クエリのパフォーマンスが重要な問題でない場合は、 `ANALYZE`を無視できます。

## 複数のBRプロセスを同時に使用して、単一のクラスタのデータを復元できますか？ {#can-i-use-multiple-br-processes-at-the-same-time-to-restore-the-data-of-a-single-cluster}

次の理由により、複数のBRプロセスを同時に使用して単一のクラスタのデータを復元する**ことは強くお勧め**しません。

-   BRがデータを復元するとき、PDのいくつかのグローバル構成を変更します。したがって、データの復元に複数のBRプロセスを同時に使用すると、これらの構成が誤って上書きされ、異常なクラスタステータスが発生する可能性があります。
-   BRはデータを復元するために多くのクラスタリソースを消費するため、実際、BRプロセスを並行して実行すると、復元速度は限られた範囲でしか向上しません。
-   データ復元のために複数のBRプロセスを並行して実行するテストは行われていないため、成功する保証はありません。

## バックアップログで<code>key locked Error</code>が報告された場合はどうすればよいですか？ {#what-should-i-do-if-the-backup-log-reports-code-key-locked-error-code}

ログのエラーメッセージ： `log - ["backup occur kv error"][error="{\"KvError\":{\"locked\":`

バックアッププロセス中にキーがロックされている場合、BRはロックを解決しようとします。このエラーがたまにしか発生しない場合、バックアップの正確性は影響を受けません。

## バックアップ操作が失敗した場合はどうすればよいですか？ {#what-should-i-do-if-a-backup-operation-fails}

ログのエラーメッセージ： `log - Error: msg:"Io(Custom { kind: AlreadyExists, error: \"[5_5359_42_123_default.sst] is already exists in /dir/backup_local/\" })"`

バックアップ操作が失敗し、上記のメッセージが表示された場合は、次のいずれかの操作を実行してから、バックアップを再開してください。

-   バックアップ用のディレクトリを変更します。たとえば、 `/dir/backup_local/`を`/dir/backup-2020-01-01/`に変更します。
-   すべてのTiKVノードとBRノードのバックアップディレクトリを削除します。

## BRのバックアップまたは復元後に、監視ノードに表示されるディスク使用量に一貫性がない場合はどうすればよいですか？ {#what-should-i-do-if-the-disk-usage-shown-on-the-monitoring-node-is-inconsistent-after-br-backup-or-restoration}

この不整合は、バックアップで使用されるデータ圧縮率が復元で使用されるデフォルトの率と異なるという事実が原因で発生します。チェックサムが成功した場合、この問題は無視できます。

## 配置ルールをクラスタに復元するとエラーが発生するのはなぜですか？ {#why-does-an-error-occur-when-i-restore-placement-rules-to-a-cluster}

v6.0.0より前では、BRは[配置ルール](/placement-rules-in-sql.md)をサポートしていません。 v6.0.0以降、BRは配置ルールをサポートし、配置ルールのバックアップおよび復元モードを制御するためのコマンドラインオプション`--with-tidb-placement-mode=strict/ignore`を導入しています。デフォルト値`strict`の場合、BRは配置ルールをインポートして検証しますが、値が`ignore`の場合はすべての配置ルールを無視します。

## BRが<code>new_collations_enabled_on_first_bootstrap</code>不一致を報告するのはなぜですか？ {#why-does-br-report-code-new-collations-enabled-on-first-bootstrap-code-mismatch}

TiDB v6.0.0以降、デフォルト値の[`new_collations_enabled_on_first_bootstrap`](/tidb-configuration-file.md#new_collations_enabled_on_first_bootstrap)が`false`から`true`に変更されました。 BRは、アップストリームクラスタの`new_collations_enabled_on_first_bootstrap`の構成をバックアップし、この構成の値がアップストリームクラスターとダウンストリームクラスターの間で一貫しているかどうかを確認します。値が一貫している場合、BRはアップストリームクラスタにバックアップされたデータをダウンストリームクラスタに安全に復元します。値に一貫性がない場合、BRはデータの復元を実行せず、エラーを報告します。

以前のバージョンのv6.0.0のTiDBクラスタにデータをバックアップし、このデータをv6.0.0以降のバージョンのTiDBクラスタに復元するとします。この状況では、 `new_collations_enabled_on_first_bootstrap`の値がアップストリームクラスターとダウンストリームクラスターの間で一貫しているかどうかを手動で確認する必要があります。

-   値が一貫している場合は、復元コマンドに`--check-requirements=false`を追加して、この構成チェックをスキップできます。
-   値に一貫性がなく、強制的に復元を実行した場合、BRはデータ検証エラーを報告します。
