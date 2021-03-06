---
title: Character Set Support
summary: Learn about the supported character sets in TiDB.
aliases: ['/docs/v2.1/character-set-and-collation/','/docs/v2.1/reference/sql/character-set/']
---

# Character Set Support

A character set is a set of symbols and encodings. A collation is a set of rules for comparing characters in a character set.

Currently, TiDB supports the following character sets:

```sql
mysql> SHOW CHARACTER SET;
+---------|---------------|-------------------|--------+
| Charset | Description   | Default collation | Maxlen |
+---------|---------------|-------------------|--------+
| utf8    | UTF-8 Unicode | utf8_bin          |      3 |
| utf8mb4 | UTF-8 Unicode | utf8mb4_bin       |      4 |
| ascii   | US ASCII      | ascii_bin         |      1 |
| latin1  | Latin1        | latin1_bin        |      1 |
| binary  | binary        | binary            |      1 |
+---------|---------------|-------------------|--------+
5 rows in set (0.00 sec)
```

> **Note:**
>
> In TiDB, utf8 is treated as utf8mb4.

## Collation support

TiDB only supports binary collations. This means that unlike MySQL, in TiDB string comparisons are both case sensitive and accent sensitive:

```sql
mysql> SELECT * FROM information_schema.collations;
+--------------------------+--------------------+------+------------+-------------+---------+
| COLLATION_NAME           | CHARACTER_SET_NAME | ID   | IS_DEFAULT | IS_COMPILED | SORTLEN |
+--------------------------+--------------------+------+------------+-------------+---------+
| big5_chinese_ci          | big5               |    1 | Yes        | Yes         |       1 |
| latin2_czech_cs          | latin2             |    2 |            | Yes         |       1 |
| dec8_swedish_ci          | dec8               |    3 | Yes        | Yes         |       1 |
| cp850_general_ci         | cp850              |    4 | Yes        | Yes         |       1 |
| latin1_german1_ci        | latin1             |    5 |            | Yes         |       1 |
| hp8_english_ci           | hp8                |    6 | Yes        | Yes         |       1 |
| koi8r_general_ci         | koi8r              |    7 | Yes        | Yes         |       1 |
| latin1_swedish_ci        | latin1             |    8 | Yes        | Yes         |       1 |
| latin2_general_ci        | latin2             |    9 | Yes        | Yes         |       1 |
| swe7_swedish_ci          | swe7               |   10 | Yes        | Yes         |       1 |
| ascii_general_ci         | ascii              |   11 | Yes        | Yes         |       1 |
| ujis_japanese_ci         | ujis               |   12 | Yes        | Yes         |       1 |
| sjis_japanese_ci         | sjis               |   13 | Yes        | Yes         |       1 |
| cp1251_bulgarian_ci      | cp1251             |   14 |            | Yes         |       1 |
| latin1_danish_ci         | latin1             |   15 |            | Yes         |       1 |
| hebrew_general_ci        | hebrew             |   16 | Yes        | Yes         |       1 |
| tis620_thai_ci           | tis620             |   18 | Yes        | Yes         |       1 |
| euckr_korean_ci          | euckr              |   19 | Yes        | Yes         |       1 |
| latin7_estonian_cs       | latin7             |   20 |            | Yes         |       1 |
| latin2_hungarian_ci      | latin2             |   21 |            | Yes         |       1 |
| koi8u_general_ci         | koi8u              |   22 | Yes        | Yes         |       1 |
| cp1251_ukrainian_ci      | cp1251             |   23 |            | Yes         |       1 |
| gb2312_chinese_ci        | gb2312             |   24 | Yes        | Yes         |       1 |
| greek_general_ci         | greek              |   25 | Yes        | Yes         |       1 |
| cp1250_general_ci        | cp1250             |   26 | Yes        | Yes         |       1 |
| latin2_croatian_ci       | latin2             |   27 |            | Yes         |       1 |
| gbk_chinese_ci           | gbk                |   28 | Yes        | Yes         |       1 |
| cp1257_lithuanian_ci     | cp1257             |   29 |            | Yes         |       1 |
| latin5_turkish_ci        | latin5             |   30 | Yes        | Yes         |       1 |
| latin1_german2_ci        | latin1             |   31 |            | Yes         |       1 |
| armscii8_general_ci      | armscii8           |   32 | Yes        | Yes         |       1 |
| utf8_general_ci          | utf8               |   33 | Yes        | Yes         |       1 |
| cp1250_czech_cs          | cp1250             |   34 |            | Yes         |       1 |
| ucs2_general_ci          | ucs2               |   35 | Yes        | Yes         |       1 |
| cp866_general_ci         | cp866              |   36 | Yes        | Yes         |       1 |
| keybcs2_general_ci       | keybcs2            |   37 | Yes        | Yes         |       1 |
| macce_general_ci         | macce              |   38 | Yes        | Yes         |       1 |
| macroman_general_ci      | macroman           |   39 | Yes        | Yes         |       1 |
| cp852_general_ci         | cp852              |   40 | Yes        | Yes         |       1 |
| latin7_general_ci        | latin7             |   41 | Yes        | Yes         |       1 |
| latin7_general_cs        | latin7             |   42 |            | Yes         |       1 |
| macce_bin                | macce              |   43 |            | Yes         |       1 |
| cp1250_croatian_ci       | cp1250             |   44 |            | Yes         |       1 |
| utf8mb4_general_ci       | utf8mb4            |   45 | Yes        | Yes         |       1 |
| utf8mb4_bin              | utf8mb4            |   46 |            | Yes         |       1 |
| latin1_bin               | latin1             |   47 |            | Yes         |       1 |
| latin1_general_ci        | latin1             |   48 |            | Yes         |       1 |
| latin1_general_cs        | latin1             |   49 |            | Yes         |       1 |
| cp1251_bin               | cp1251             |   50 |            | Yes         |       1 |
| cp1251_general_ci        | cp1251             |   51 | Yes        | Yes         |       1 |
| cp1251_general_cs        | cp1251             |   52 |            | Yes         |       1 |
| macroman_bin             | macroman           |   53 |            | Yes         |       1 |
| utf16_general_ci         | utf16              |   54 | Yes        | Yes         |       1 |
| utf16_bin                | utf16              |   55 |            | Yes         |       1 |
| utf16le_general_ci       | utf16le            |   56 | Yes        | Yes         |       1 |
| cp1256_general_ci        | cp1256             |   57 | Yes        | Yes         |       1 |
| cp1257_bin               | cp1257             |   58 |            | Yes         |       1 |
| cp1257_general_ci        | cp1257             |   59 | Yes        | Yes         |       1 |
| utf32_general_ci         | utf32              |   60 | Yes        | Yes         |       1 |
| utf32_bin                | utf32              |   61 |            | Yes         |       1 |
| utf16le_bin              | utf16le            |   62 |            | Yes         |       1 |
| binary                   | binary             |   63 | Yes        | Yes         |       1 |
| armscii8_bin             | armscii8           |   64 |            | Yes         |       1 |
| ascii_bin                | ascii              |   65 |            | Yes         |       1 |
| cp1250_bin               | cp1250             |   66 |            | Yes         |       1 |
| cp1256_bin               | cp1256             |   67 |            | Yes         |       1 |
| cp866_bin                | cp866              |   68 |            | Yes         |       1 |
| dec8_bin                 | dec8               |   69 |            | Yes         |       1 |
| greek_bin                | greek              |   70 |            | Yes         |       1 |
| hebrew_bin               | hebrew             |   71 |            | Yes         |       1 |
| hp8_bin                  | hp8                |   72 |            | Yes         |       1 |
| keybcs2_bin              | keybcs2            |   73 |            | Yes         |       1 |
| koi8r_bin                | koi8r              |   74 |            | Yes         |       1 |
| koi8u_bin                | koi8u              |   75 |            | Yes         |       1 |
| latin2_bin               | latin2             |   77 |            | Yes         |       1 |
| latin5_bin               | latin5             |   78 |            | Yes         |       1 |
| latin7_bin               | latin7             |   79 |            | Yes         |       1 |
| cp850_bin                | cp850              |   80 |            | Yes         |       1 |
| cp852_bin                | cp852              |   81 |            | Yes         |       1 |
| swe7_bin                 | swe7               |   82 |            | Yes         |       1 |
| utf8_bin                 | utf8               |   83 |            | Yes         |       1 |
| big5_bin                 | big5               |   84 |            | Yes         |       1 |
| euckr_bin                | euckr              |   85 |            | Yes         |       1 |
| gb2312_bin               | gb2312             |   86 |            | Yes         |       1 |
| gbk_bin                  | gbk                |   87 |            | Yes         |       1 |
| sjis_bin                 | sjis               |   88 |            | Yes         |       1 |
| tis620_bin               | tis620             |   89 |            | Yes         |       1 |
| ucs2_bin                 | ucs2               |   90 |            | Yes         |       1 |
| ujis_bin                 | ujis               |   91 |            | Yes         |       1 |
| geostd8_general_ci       | geostd8            |   92 | Yes        | Yes         |       1 |
| geostd8_bin              | geostd8            |   93 |            | Yes         |       1 |
| latin1_spanish_ci        | latin1             |   94 |            | Yes         |       1 |
| cp932_japanese_ci        | cp932              |   95 | Yes        | Yes         |       1 |
| cp932_bin                | cp932              |   96 |            | Yes         |       1 |
| eucjpms_japanese_ci      | eucjpms            |   97 | Yes        | Yes         |       1 |
| eucjpms_bin              | eucjpms            |   98 |            | Yes         |       1 |
| cp1250_polish_ci         | cp1250             |   99 |            | Yes         |       1 |
| utf16_unicode_ci         | utf16              |  101 |            | Yes         |       1 |
| utf16_icelandic_ci       | utf16              |  102 |            | Yes         |       1 |
| utf16_latvian_ci         | utf16              |  103 |            | Yes         |       1 |
| utf16_romanian_ci        | utf16              |  104 |            | Yes         |       1 |
| utf16_slovenian_ci       | utf16              |  105 |            | Yes         |       1 |
| utf16_polish_ci          | utf16              |  106 |            | Yes         |       1 |
| utf16_estonian_ci        | utf16              |  107 |            | Yes         |       1 |
| utf16_spanish_ci         | utf16              |  108 |            | Yes         |       1 |
| utf16_swedish_ci         | utf16              |  109 |            | Yes         |       1 |
| utf16_turkish_ci         | utf16              |  110 |            | Yes         |       1 |
| utf16_czech_ci           | utf16              |  111 |            | Yes         |       1 |
| utf16_danish_ci          | utf16              |  112 |            | Yes         |       1 |
| utf16_lithuanian_ci      | utf16              |  113 |            | Yes         |       1 |
| utf16_slovak_ci          | utf16              |  114 |            | Yes         |       1 |
| utf16_spanish2_ci        | utf16              |  115 |            | Yes         |       1 |
| utf16_roman_ci           | utf16              |  116 |            | Yes         |       1 |
| utf16_persian_ci         | utf16              |  117 |            | Yes         |       1 |
| utf16_esperanto_ci       | utf16              |  118 |            | Yes         |       1 |
| utf16_hungarian_ci       | utf16              |  119 |            | Yes         |       1 |
| utf16_sinhala_ci         | utf16              |  120 |            | Yes         |       1 |
| utf16_german2_ci         | utf16              |  121 |            | Yes         |       1 |
| utf16_croatian_ci        | utf16              |  122 |            | Yes         |       1 |
| utf16_unicode_520_ci     | utf16              |  123 |            | Yes         |       1 |
| utf16_vietnamese_ci      | utf16              |  124 |            | Yes         |       1 |
| ucs2_unicode_ci          | ucs2               |  128 |            | Yes         |       1 |
| ucs2_icelandic_ci        | ucs2               |  129 |            | Yes         |       1 |
| ucs2_latvian_ci          | ucs2               |  130 |            | Yes         |       1 |
| ucs2_romanian_ci         | ucs2               |  131 |            | Yes         |       1 |
| ucs2_slovenian_ci        | ucs2               |  132 |            | Yes         |       1 |
| ucs2_polish_ci           | ucs2               |  133 |            | Yes         |       1 |
| ucs2_estonian_ci         | ucs2               |  134 |            | Yes         |       1 |
| ucs2_spanish_ci          | ucs2               |  135 |            | Yes         |       1 |
| ucs2_swedish_ci          | ucs2               |  136 |            | Yes         |       1 |
| ucs2_turkish_ci          | ucs2               |  137 |            | Yes         |       1 |
| ucs2_czech_ci            | ucs2               |  138 |            | Yes         |       1 |
| ucs2_danish_ci           | ucs2               |  139 |            | Yes         |       1 |
| ucs2_lithuanian_ci       | ucs2               |  140 |            | Yes         |       1 |
| ucs2_slovak_ci           | ucs2               |  141 |            | Yes         |       1 |
| ucs2_spanish2_ci         | ucs2               |  142 |            | Yes         |       1 |
| ucs2_roman_ci            | ucs2               |  143 |            | Yes         |       1 |
| ucs2_persian_ci          | ucs2               |  144 |            | Yes         |       1 |
| ucs2_esperanto_ci        | ucs2               |  145 |            | Yes         |       1 |
| ucs2_hungarian_ci        | ucs2               |  146 |            | Yes         |       1 |
| ucs2_sinhala_ci          | ucs2               |  147 |            | Yes         |       1 |
| ucs2_german2_ci          | ucs2               |  148 |            | Yes         |       1 |
| ucs2_croatian_ci         | ucs2               |  149 |            | Yes         |       1 |
| ucs2_unicode_520_ci      | ucs2               |  150 |            | Yes         |       1 |
| ucs2_vietnamese_ci       | ucs2               |  151 |            | Yes         |       1 |
| ucs2_general_mysql500_ci | ucs2               |  159 |            | Yes         |       1 |
| utf32_unicode_ci         | utf32              |  160 |            | Yes         |       1 |
| utf32_icelandic_ci       | utf32              |  161 |            | Yes         |       1 |
| utf32_latvian_ci         | utf32              |  162 |            | Yes         |       1 |
| utf32_romanian_ci        | utf32              |  163 |            | Yes         |       1 |
| utf32_slovenian_ci       | utf32              |  164 |            | Yes         |       1 |
| utf32_polish_ci          | utf32              |  165 |            | Yes         |       1 |
| utf32_estonian_ci        | utf32              |  166 |            | Yes         |       1 |
| utf32_spanish_ci         | utf32              |  167 |            | Yes         |       1 |
| utf32_swedish_ci         | utf32              |  168 |            | Yes         |       1 |
| utf32_turkish_ci         | utf32              |  169 |            | Yes         |       1 |
| utf32_czech_ci           | utf32              |  170 |            | Yes         |       1 |
| utf32_danish_ci          | utf32              |  171 |            | Yes         |       1 |
| utf32_lithuanian_ci      | utf32              |  172 |            | Yes         |       1 |
| utf32_slovak_ci          | utf32              |  173 |            | Yes         |       1 |
| utf32_spanish2_ci        | utf32              |  174 |            | Yes         |       1 |
| utf32_roman_ci           | utf32              |  175 |            | Yes         |       1 |
| utf32_persian_ci         | utf32              |  176 |            | Yes         |       1 |
| utf32_esperanto_ci       | utf32              |  177 |            | Yes         |       1 |
| utf32_hungarian_ci       | utf32              |  178 |            | Yes         |       1 |
| utf32_sinhala_ci         | utf32              |  179 |            | Yes         |       1 |
| utf32_german2_ci         | utf32              |  180 |            | Yes         |       1 |
| utf32_croatian_ci        | utf32              |  181 |            | Yes         |       1 |
| utf32_unicode_520_ci     | utf32              |  182 |            | Yes         |       1 |
| utf32_vietnamese_ci      | utf32              |  183 |            | Yes         |       1 |
| utf8_unicode_ci          | utf8               |  192 |            | Yes         |       1 |
| utf8_icelandic_ci        | utf8               |  193 |            | Yes         |       1 |
| utf8_latvian_ci          | utf8               |  194 |            | Yes         |       1 |
| utf8_romanian_ci         | utf8               |  195 |            | Yes         |       1 |
| utf8_slovenian_ci        | utf8               |  196 |            | Yes         |       1 |
| utf8_polish_ci           | utf8               |  197 |            | Yes         |       1 |
| utf8_estonian_ci         | utf8               |  198 |            | Yes         |       1 |
| utf8_spanish_ci          | utf8               |  199 |            | Yes         |       1 |
| utf8_swedish_ci          | utf8               |  200 |            | Yes         |       1 |
| utf8_turkish_ci          | utf8               |  201 |            | Yes         |       1 |
| utf8_czech_ci            | utf8               |  202 |            | Yes         |       1 |
| utf8_danish_ci           | utf8               |  203 |            | Yes         |       1 |
| utf8_lithuanian_ci       | utf8               |  204 |            | Yes         |       1 |
| utf8_slovak_ci           | utf8               |  205 |            | Yes         |       1 |
| utf8_spanish2_ci         | utf8               |  206 |            | Yes         |       1 |
| utf8_roman_ci            | utf8               |  207 |            | Yes         |       1 |
| utf8_persian_ci          | utf8               |  208 |            | Yes         |       1 |
| utf8_esperanto_ci        | utf8               |  209 |            | Yes         |       1 |
| utf8_hungarian_ci        | utf8               |  210 |            | Yes         |       1 |
| utf8_sinhala_ci          | utf8               |  211 |            | Yes         |       1 |
| utf8_german2_ci          | utf8               |  212 |            | Yes         |       1 |
| utf8_croatian_ci         | utf8               |  213 |            | Yes         |       1 |
| utf8_unicode_520_ci      | utf8               |  214 |            | Yes         |       1 |
| utf8_vietnamese_ci       | utf8               |  215 |            | Yes         |       1 |
| utf8_general_mysql500_ci | utf8               |  223 |            | Yes         |       1 |
| utf8mb4_unicode_ci       | utf8mb4            |  224 |            | Yes         |       1 |
| utf8mb4_icelandic_ci     | utf8mb4            |  225 |            | Yes         |       1 |
| utf8mb4_latvian_ci       | utf8mb4            |  226 |            | Yes         |       1 |
| utf8mb4_romanian_ci      | utf8mb4            |  227 |            | Yes         |       1 |
| utf8mb4_slovenian_ci     | utf8mb4            |  228 |            | Yes         |       1 |
| utf8mb4_polish_ci        | utf8mb4            |  229 |            | Yes         |       1 |
| utf8mb4_estonian_ci      | utf8mb4            |  230 |            | Yes         |       1 |
| utf8mb4_spanish_ci       | utf8mb4            |  231 |            | Yes         |       1 |
| utf8mb4_swedish_ci       | utf8mb4            |  232 |            | Yes         |       1 |
| utf8mb4_turkish_ci       | utf8mb4            |  233 |            | Yes         |       1 |
| utf8mb4_czech_ci         | utf8mb4            |  234 |            | Yes         |       1 |
| utf8mb4_danish_ci        | utf8mb4            |  235 |            | Yes         |       1 |
| utf8mb4_lithuanian_ci    | utf8mb4            |  236 |            | Yes         |       1 |
| utf8mb4_slovak_ci        | utf8mb4            |  237 |            | Yes         |       1 |
| utf8mb4_spanish2_ci      | utf8mb4            |  238 |            | Yes         |       1 |
| utf8mb4_roman_ci         | utf8mb4            |  239 |            | Yes         |       1 |
| utf8mb4_persian_ci       | utf8mb4            |  240 |            | Yes         |       1 |
| utf8mb4_esperanto_ci     | utf8mb4            |  241 |            | Yes         |       1 |
| utf8mb4_hungarian_ci     | utf8mb4            |  242 |            | Yes         |       1 |
| utf8mb4_sinhala_ci       | utf8mb4            |  243 |            | Yes         |       1 |
| utf8mb4_german2_ci       | utf8mb4            |  244 |            | Yes         |       1 |
| utf8mb4_croatian_ci      | utf8mb4            |  245 |            | Yes         |       1 |
| utf8mb4_unicode_520_ci   | utf8mb4            |  246 |            | Yes         |       1 |
| utf8mb4_vietnamese_ci    | utf8mb4            |  247 |            | Yes         |       1 |
| utf8mb4_0900_ai_ci       | utf8mb4            |  255 |            | Yes         |       1 |
+--------------------------+--------------------+------+------------+-------------+---------+
220 rows in set (0.00 sec)

mysql> SHOW COLLATION WHERE Charset = 'utf8mb4';
+------------------------+---------+------+---------+----------+---------+
| Collation              | Charset | Id   | Default | Compiled | Sortlen |
+------------------------+---------+------+---------+----------+---------+
| utf8mb4_general_ci     | utf8mb4 |   45 | Yes     | Yes      |       1 |
| utf8mb4_bin            | utf8mb4 |   46 |         | Yes      |       1 |
| utf8mb4_unicode_ci     | utf8mb4 |  224 |         | Yes      |       1 |
| utf8mb4_icelandic_ci   | utf8mb4 |  225 |         | Yes      |       1 |
| utf8mb4_latvian_ci     | utf8mb4 |  226 |         | Yes      |       1 |
| utf8mb4_romanian_ci    | utf8mb4 |  227 |         | Yes      |       1 |
| utf8mb4_slovenian_ci   | utf8mb4 |  228 |         | Yes      |       1 |
| utf8mb4_polish_ci      | utf8mb4 |  229 |         | Yes      |       1 |
| utf8mb4_estonian_ci    | utf8mb4 |  230 |         | Yes      |       1 |
| utf8mb4_spanish_ci     | utf8mb4 |  231 |         | Yes      |       1 |
| utf8mb4_swedish_ci     | utf8mb4 |  232 |         | Yes      |       1 |
| utf8mb4_turkish_ci     | utf8mb4 |  233 |         | Yes      |       1 |
| utf8mb4_czech_ci       | utf8mb4 |  234 |         | Yes      |       1 |
| utf8mb4_danish_ci      | utf8mb4 |  235 |         | Yes      |       1 |
| utf8mb4_lithuanian_ci  | utf8mb4 |  236 |         | Yes      |       1 |
| utf8mb4_slovak_ci      | utf8mb4 |  237 |         | Yes      |       1 |
| utf8mb4_spanish2_ci    | utf8mb4 |  238 |         | Yes      |       1 |
| utf8mb4_roman_ci       | utf8mb4 |  239 |         | Yes      |       1 |
| utf8mb4_persian_ci     | utf8mb4 |  240 |         | Yes      |       1 |
| utf8mb4_esperanto_ci   | utf8mb4 |  241 |         | Yes      |       1 |
| utf8mb4_hungarian_ci   | utf8mb4 |  242 |         | Yes      |       1 |
| utf8mb4_sinhala_ci     | utf8mb4 |  243 |         | Yes      |       1 |
| utf8mb4_german2_ci     | utf8mb4 |  244 |         | Yes      |       1 |
| utf8mb4_croatian_ci    | utf8mb4 |  245 |         | Yes      |       1 |
| utf8mb4_unicode_520_ci | utf8mb4 |  246 |         | Yes      |       1 |
| utf8mb4_vietnamese_ci  | utf8mb4 |  247 |         | Yes      |       1 |
| utf8mb4_0900_ai_ci     | utf8mb4 |  255 |         | Yes      |       1 |
+------------------------+---------+------+---------+----------+---------+
27 rows in set (0.00 sec)
```

> **Warning:**
>
> TiDB incorrectly treats latin1 as a subset of utf8. This can lead to unexpected behaviors when you store characters that differ between latin1 and utf8 encodings. It is strongly recommended to the utf8mb4 character set. See [TiDB #18955](https://github.com/pingcap/tidb/issues/18955) for more details.

> **Note:**
>
> The output of `SHOW COLLATION` and `SELECT * FROM information_schema.collations` includes collations which TiDB does not support. This is fixed in TiDB 3.0, where only supported collations are shown.

For compatibility with MySQL, TiDB will allow other collation names to be used:

```sql
mysql> CREATE TABLE t1 (a INT NOT NULL PRIMARY KEY AUTO_INCREMENT, b VARCHAR(10)) COLLATE utf8mb4_unicode_520_ci;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO t1 VALUES (1, 'a');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM t1 WHERE b = 'a';
+---+------+
| a | b    |
+---+------+
| 1 | a    |
+---+------+
1 row in set (0.00 sec)

mysql> SELECT * FROM t1 WHERE b = 'A';
Empty set (0.00 sec)

mysql> SHOW CREATE TABLE t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `a` int(11) NOT NULL AUTO_INCREMENT,
  `b` varchar(10) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin DEFAULT NULL,
  PRIMARY KEY (`a`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_520_ci AUTO_INCREMENT=30002
1 row in set (0.00 sec)
```

## Collation naming conventions

The collation names in TiDB follow these conventions:

- The prefix of a collation is its corresponding character set, generally followed by one or more suffixes indicating other collation characteristic.  For example, `utf8_general_ci` and `latin1_swedish_ci` are collations for the utf8 and latin1 character sets, respectively. The `binary` character set has a single collation, also named `binary`, with no suffixes.
- A language-specific collation includes a language name. For example, `utf8_turkish_ci` and `utf8_hungarian_ci` sort characters for the utf8 character set using the rules of Turkish and Hungarian, respectively.
- Collation suffixes indicate whether a collation is case and accent sensitive, or binary. The following table shows the suffixes used to indicate these characteristics.

    | Suffix | Meaning            |
    | :----- | :----------------- |
    | \_ai   | Accent insensitive |
    | \_as   | Accent sensitive   |
    | \_ci   | Case insensitive   |
    | \_cs   | Case sensitive     |
    | \_bin  | Binary             |

> **Note:**
>
> Currently, TiDB only supports some of the collations in the above table.

## Database character set and collation

Each database has a character set and a collation. You can use the `CREATE DATABASE` statement to specify the database character set and collation:

```sql
CREATE DATABASE db_name
    [[DEFAULT] CHARACTER SET charset_name]
    [[DEFAULT] COLLATE collation_name]
```

Where `DATABASE` can be replaced with `SCHEMA`.

Different databases can use different character sets and collations. Use the `character_set_database` and  `collation_database` to see the character set and collation of the current database:

```sql
mysql> CREATE SCHEMA test1 CHARACTER SET utf8 COLLATE utf8_general_ci;
Query OK, 0 rows affected (0.09 sec)

mysql> USE test1;
Database changed
mysql> SELECT @@character_set_database, @@collation_database;
+--------------------------|----------------------+
| @@character_set_database | @@collation_database |
+--------------------------|----------------------+
| utf8                     | utf8_general_ci      |
+--------------------------|----------------------+
1 row in set (0.00 sec)

mysql> CREATE SCHEMA test2 CHARACTER SET latin1 COLLATE latin1_general_ci;
Query OK, 0 rows affected (0.09 sec)

mysql> USE test2;
Database changed
mysql> SELECT @@character_set_database, @@collation_database;
+--------------------------|----------------------+
| @@character_set_database | @@collation_database |
+--------------------------|----------------------+
| latin1                   | latin1_general_ci    |
+--------------------------|----------------------+
1 row in set (0.00 sec)
```

You can also see the two values in INFORMATION_SCHEMA:

```sql
SELECT DEFAULT_CHARACTER_SET_NAME, DEFAULT_COLLATION_NAME
FROM INFORMATION_SCHEMA.SCHEMATA WHERE SCHEMA_NAME = 'db_name';
```

## Table character set and collation

You can use the following statement to specify the character set and collation for tables:

```sql
CREATE TABLE tbl_name (column_list)
    [[DEFAULT] CHARACTER SET charset_name]
    [COLLATE collation_name]]

ALTER TABLE tbl_name
    [[DEFAULT] CHARACTER SET charset_name]
    [COLLATE collation_name]
```

For example:

```sql
mysql> CREATE TABLE t1(a int) CHARACTER SET utf8 COLLATE utf8_general_ci;
Query OK, 0 rows affected (0.08 sec)
```

The table character set and collation are used as the default values for column definitions if the column character set and collation are not specified in individual column definitions.

## Column character set and collation

See the following table for the character set and collation syntax for columns:

```sql
col_name {CHAR | VARCHAR | TEXT} (col_length)
    [CHARACTER SET charset_name]
    [COLLATE collation_name]

col_name {ENUM | SET} (val_list)
    [CHARACTER SET charset_name]
    [COLLATE collation_name]
```

## Connection character sets and collations

- The server character set and collation are the values of the `character_set_server` and `collation_server` system variables.

- The character set and collation of the default database are the values of the `character_set_database` and `collation_database` system variables.

    You can use `character_set_connection` and `collation_connection` to specify the character set and collation for each connection.

    The `character_set_client` variable is to set the client character set. Before returning the result, the `character_set_results` system variable indicates the character set in which the server returns query results to the client, including the metadata of the result.

You can use the following statement to specify a particular collation that is related to the client:

- `SET NAMES 'charset_name' [COLLATE 'collation_name']`

    `SET NAMES` indicates what character set the client will use to send SQL statements to the server. `SET NAMES utf8` indicates that all the requests from the client use utf8, as well as the results from the server.

    The `SET NAMES 'charset_name'` statement is equivalent to the following statement combination:

    ```sql
    SET character_set_client = charset_name;
    SET character_set_results = charset_name;
    SET character_set_connection = charset_name;
    ```

    `COLLATE` is optional, if absent, the default collation of the `charset_name` is used.

- `SET CHARACTER SET 'charset_name'`

    Similar to `SET NAMES`, the `SET NAMES 'charset_name'` statement is equivalent to the following statement combination:

    ```sql
    SET character_set_client = charset_name;
    SET character_set_results = charset_name;
    SET collation_connection = @@collation_database;
    ```

## Validity check of characters

For the specified `utf8` or `utf8mb4` character set, TiDB only supports the valid `utf8` character, and reports the `incorrect utf8 value` error when the character is invalid. This validity check of characters in TiDB is compatible with MySQL 8.0 but incompatible with MySQL 5.7 or earlier versions.

To disable this error reporting, use `set @@tidb_skip_utf8_check=1;` to skip the character check.

For more information, see [Connection Character Sets and Collations in MySQL](https://dev.mysql.com/doc/refman/5.7/en/charset-connection.html).
