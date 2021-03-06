---
title: Precision Math
summary: Learn about the precision math in TiDB.
---

# 精密計算 {#precision-math}

TiDBでの高精度の数学サポートは、MySQLと一貫性があります。詳細については、 [MySQLの精密計算](https://dev.mysql.com/doc/refman/5.7/en/precision-math.html)を参照してください。

## 数値タイプ {#numeric-types}

正確な値の演算の精度計算の範囲には、正確な値のデータ型（整数型とDECIMAL型）と正確な値の数値リテラルが含まれます。近似値のデータ型と数値リテラルは、浮動小数点数として処理されます。

正確な値の数値リテラルには、整数部分または小数部分、あるいはその両方があります。それらは署名されるかもしれません。 `-5` `-6.78` `1` `+9.10` `.2` `3.4`

近似値の数値リテラルは、仮数と指数を使用した科学的記数法（10の累乗）で表されます。いずれかまたは両方の部分に署名することができます。 `1.2E-3` `-1.2E3` `1.2E3` `-1.2E-3`

似ているように見える2つの数値は、異なる方法で処理される場合があります。たとえば、 `2.34`は正確な値（固定小数点）の数値ですが、 `2.34E0`は近似値（浮動小数点）の数値です。

DECIMALデータ型は固定小数点型であり、計算は正確です。 FLOATおよびDOUBLEデータ型は浮動小数点型であり、計算は概算です。

## DECIMALデータ型の特性 {#decimal-data-type-characteristics}

このセクションでは、DECIMALデータ型（およびその同義語）の特性に関する次のトピックについて説明します。

-   最大桁数
-   ストレージ形式
-   ストレージ要件

DECIMAL列の宣言構文は`DECIMAL(M,D)`です。引数の値の範囲は次のとおりです。

-   Mは最大桁数（精度）です。 1 &lt;= M&lt;=65。
-   Dは小数点（目盛り）の右側の桁数です。 1 &lt;= D &lt;= 30であり、DはM以下でなければなりません。

Mの最大値65は、DECIMAL値の計算が65桁まで正確であることを意味します。この65桁の精度の制限は、正確な値の数値リテラルにも適用されます。

DECIMAL列の値は、10進数の9桁を4バイトにパックするバイナリ形式を使用して格納されます。各値の整数部分と小数部分のストレージ要件は、個別に決定されます。 9桁の倍数ごとに4バイトが必要であり、残りの桁には4バイトの何分の1かが必要です。残りの桁に必要なストレージは、次の表に示されています。

| 残りの数字 | バイト数 |
| ----- | ---- |
| 0     | 0    |
| 1–2   | 1    |
| 3–4   | 2    |
| 5–6   | 3    |
| 7–9   | 4    |

たとえば、 `DECIMAL(18,9)`列の小数点の両側に9桁があるため、整数部分と小数部分にはそれぞれ4バイトが必要です。 `DECIMAL(20,6)`列には、14桁の整数と6桁の小数桁があります。整数桁には、9桁で4バイト、残りの5桁で3バイトが必要です。 6桁の小数部には3バイトが必要です。

DECIMAL列には、先頭の`+`文字または`-`文字または先頭の`0`桁は格納されません。 `DECIMAL(5,1)`列に`+0003.1`を挿入すると、 `3.1`として格納されます。負の数の場合、文字通りの`-`文字は格納されません。

DECIMAL列は、列定義によって示される範囲よりも大きい値を許可しません。たとえば、 `DECIMAL(3,0)`列は`-999`の範囲をサポートし`999` 。 `DECIMAL(M,D)`列では、小数点の左側に最大`M - D`桁を使用できます。

DECIMAL値の内部形式の詳細については、TiDBソースコードの[`mydecimal.go`](https://github.com/pingcap/tidb/blob/master/types/mydecimal.go)を参照してください。

## 式の処理 {#expression-handling}

精度の高い式の場合、TiDBは可能な限り正確な値の数値を使用します。たとえば、比較の数値は、値を変更せずに指定されたとおりに使用されます。厳密なSQLモードでは、正確なデータ型を列に追加すると、列の範囲内にある場合は、その正確な値とともに数値が挿入されます。取得されると、値は挿入されたものと同じになります。厳密なSQLモードが有効になっていない場合、TIDBではINSERTの切り捨てが許可されます。

数式の処理方法は、数式の値によって異なります。

-   式に近似値が含まれている場合、結果は近似値になります。 TiDBは、浮動小数点演算を使用して式を評価します。
-   式に近似値が含まれていない場合、つまり正確な値のみが含まれている場合、および正確な値に小数部分が含まれている場合、式はDECIMALの正確な算術を使用して評価され、65桁の精度になります。
-   それ以外の場合、式には整数値のみが含まれます。式は正確です。 TiDBは整数演算を使用して式を評価し、BIGINT（64ビット）と同じ精度を持っています。

数式に文字列が含まれている場合、文字列は倍精度浮動小数点値に変換され、式の結果は概算になります。

数値列への挿入は、SQLモードの影響を受けます。以下の説明では、厳密モードと`ERROR_FOR_DIVISION_BY_ZERO`について説明します。すべての制限をオンにするには、厳密なモード値と`ERROR_FOR_DIVISION_BY_ZERO`の両方を含む`TRADITIONAL`モードを使用するだけです。

```sql
SET sql_mode = 'TRADITIONAL`;
```

数値が正確な型の列（DECIMALまたは整数）に挿入される場合、列の範囲内であれば正確な値で挿入されます。この番号の場合：

-   値の小数部の桁数が多すぎると、丸めが発生し、警告が生成されます。
-   値の整数部分の桁数が多すぎる場合は、値が大きすぎるため、次のように処理されます。
    -   strictモードが有効になっていない場合、値は最も近い有効な値に切り捨てられ、警告が生成されます。
    -   ストリクトモードが有効になっている場合、オーバーフローエラーが発生します。

文字列を数値列に挿入するために、文字列に数値以外の内容が含まれている場合、TiDBは文字列から数値への変換を次のように処理します。

-   厳密モードでは、数字で始まらない文字列（空の文字列を含む）を数字として使用することはできません。エラー、または警告が発生します。
-   数字で始まる文字列は変換できますが、末尾の非数値部分は切り捨てられます。厳密モードでは、切り捨てられた部分にスペース以外のものが含まれていると、エラーまたは警告が発生します。

デフォルトでは、0による除算の結果はNULLであり、警告はありません。 SQLモードを適切に設定することにより、0による除算を制限できます。 `ERROR_FOR_DIVISION_BY_ZERO` SQLモードを有効にすると、TiDBは0による除算を異なる方法で処理します。

-   厳密モードでは、挿入と更新が禁止され、エラーが発生します。
-   厳密モードでない場合は、警告が発生します。

次のSQLステートメントで：

```sql
INSERT INTO t SET i = 1/0;
```

次の結果は、さまざまなSQLモードで返されます。

| `sql_mode`値                      | 結果                           |
| :------------------------------- | :--------------------------- |
| &#39;&#39;                       | 警告もエラーもありません。 iはNULLに設定されます。 |
| 厳しい                              | 警告もエラーもありません。 iはNULLに設定されます。 |
| `ERROR_FOR_DIVISION_BY_ZERO`     | 警告、エラーなし。 iはNULLに設定されます。     |
| 厳格、 `ERROR_FOR_DIVISION_BY_ZERO` | エラー;行は挿入されません。               |

## 丸め動作 {#rounding-behavior}

`ROUND()`関数の結果は、その引数が正確であるか近似であるかによって異なります。

-   正確な値の数値の場合、 `ROUND()`関数は「切り上げ」ルールを使用します。
-   概算値の数値の場合、TiDBの結果はMySQLの結果とは異なります。

    ```sql
    TiDB > SELECT ROUND(2.5), ROUND(25E-1);
    +------------+--------------+
    | ROUND(2.5) | ROUND(25E-1) |
    +------------+--------------+
    |          3 |            3 |
    +------------+--------------+
    1 row in set (0.00 sec)
    ```

DECIMALまたは整数列への挿入の場合、丸めには[ゼロから半分離れたところに丸める](https://en.wikipedia.org/wiki/Rounding#Round_half_away_from_zero)が使用されます。

```sql
TiDB > CREATE TABLE t (d DECIMAL(10,0));
Query OK, 0 rows affected (0.01 sec)

TiDB > INSERT INTO t VALUES(2.5),(2.5E0);
Query OK, 2 rows affected, 2 warnings (0.00 sec)

TiDB > SELECT d FROM t;
+------+
| d    |
+------+
|    3 |
|    3 |
+------+
2 rows in set (0.00 sec)
```
