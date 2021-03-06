---
title: tiup cluster list
---

# tiup cluster list {#tiup-cluster-list}

tiup-clusterは、同じ制御マシンを使用した複数のクラスターのデプロイをサポートします。 `tiup cluster list`コマンドは、このコントロールマシンを使用して現在ログインしているユーザーによって展開されたすべてのクラスターを出力します。

> **ノート：**
>
> デプロイされたクラスタデータはデフォルトで`~/.tiup/storage/cluster/clusters/`ディレクトリに保存されるため、同じコントロールマシン上で、現在ログインしているユーザーは他のユーザーによってデプロイされたクラスターを表示できません。

## 構文 {#syntax}

```shell
tiup cluster list [flags]
```

## オプション {#options}

### -h、-help {#h-help}

-   ヘルプ情報を出力します。
-   データ型： `BOOLEAN`
-   このオプションはデフォルトで無効になっており、デフォルト値は`false`です。このオプションを有効にするには、このオプションをコマンドに追加して、 `true`の値を渡すか、値を渡さないようにします。

## 出力 {#outputs}

次のフィールドを含むテーブルを出力します。

-   名前：クラスタ名
-   ユーザー：デプロイメントユーザー
-   バージョン：クラスタバージョン
-   パス：コントロールマシン上のクラスタ展開データのパス
-   PrivateKey：クラスタの接続に使用される秘密鍵のパス

[&lt;&lt;前のページに戻る-TiUPクラスターコマンドリスト](/tiup/tiup-component-cluster.md#command-list)
