---
title: TiUP Terminology and Concepts
summary: Explain the terms and concepts of TiUP.
---

# TiUPの用語と概念 {#tiup-terminology-and-concepts}

このドキュメントでは、TiUPの重要な用語と概念について説明します。

## TiUPコンポーネント {#tiup-components}

TiUPプログラムには、コンポーネントをダウンロード、更新、およびアンインストールするためのコマンドがいくつか含まれています。 TiUPは、さまざまなコンポーネントで関数を拡張します。**コンポーネント**は、実行可能なプログラムまたはスクリプトです。 `tiup <component>`を介してコンポーネントを実行する場合、TiUPは環境変数のセットを追加し、プログラムのデータディレクトリを作成してから、プログラムを実行します。

`tiup <component>`コマンドを実行することにより、TiUPでサポートされているコンポーネントを実行できます。実行中のロジックは次のとおりです。

-   コンポーネントのバージョンを`tiup <component>[:version]`で指定した場合：

    -   コンポーネントにローカルにインストールされているバージョンがない場合、TiUPはミラーサーバーから最新の安定バージョンをダウンロードします。
    -   コンポーネントに1つ以上のバージョンがローカルにインストールされているが、指定されたバージョンがない場合、TiUPは指定されたバージョンをミラーサーバーからダウンロードします。
    -   指定されたバージョンのコンポーネントがローカルにインストールされている場合、TiUPはインストールされたバージョンを実行するように環境変数を設定します。

-   コンポーネントを`tiup <component>`まで実行し、バージョンを指定しない場合：

    -   コンポーネントにローカルにインストールされているバージョンがない場合、TiUPはミラーサーバーから最新の安定バージョンをダウンロードします。
    -   1つ以上のバージョンがローカルにインストールされている場合、TiUPは、インストールされている最新バージョンを実行するように環境変数を設定します。

## TiUPミラー {#tiup-mirrors}

TiUPのすべてのコンポーネントは、TiUPミラーからダウンロードされます。 TiUPミラーには、各コンポーネントのTARパッケージと、対応するメタ情報（バージョン、エントリ起動ファイル、チェックサム）が含まれています。 TiUPは、デフォルトでPingCAPの公式ミラーを使用します。 `TIUP_MIRRORS`の環境変数を使用してミラーソースをカスタマイズできます。

TiUPミラーは、ローカルファイルディレクトリまたはオンラインHTTPサーバーにすることができます。

-   `TIUP_MIRRORS=/path/to/local tiup list`
-   `TIUP_MIRRORS=https://private-mirrors.example.com tiup list`
