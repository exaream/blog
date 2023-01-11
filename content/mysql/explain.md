---
date: 2020-01-01
lastmod: ["lastmod"]
title: MySQL EXPLAIN
LinkTitle: EXPLAIN
description: MySQL EXPLAIN
tags: ["MySQL", "SQL", "EXPLAIN"]
---

# MySQL EXPLAIN

## 前提

- MySQL 5.7（エネルギー開発部で現在メインで使用しているMySQLバージョン）
- Clustrix は挙動が異なる可能性があるため対象外。

## SQLチューニングの手順

- スロークエリ（重いクエリ）を抽出。
  - スロークエリ・ログに記録。
  - スロークエリを集計。
- 実行計画（MySQLが内部的に立てる実行手順）=「MySQLの意思表示」を確認。
  - EXPLAIN を、`SELECT`・`DELETE`・`INSERT`・`REPLACE`・`UPDATE` の先頭につけて実行。（MySQL5.6.3より前は `SELECT` のみ）
- チューニング。
  - SQLの改善。
  - `INDEX`（テーブルの索引）の追加。日本語では「インデックスを張（は）る」と言う場合が多い。
  - テーブル構成の見直し。

## スロークエリーログ

### 設定の項目

|設定項目|設定内容|
|---|---|
|`slow_query_log`|スロークエリーログを出力する際に必要となる設定。デフォルト値は0。|
|`long_query_time`|スロークエリーログを出力するための閾値（しきいち）の設定。0秒~360024365の範囲で指定。小数点以下の値を指定することでμs単位まで指定が可能。0秒を指定することで発行されたSQLを全部保存することも可能。|
|`log_queries_not_using_indexes`|インデックスが未使用なクエリに関して記録をする設定。|
|`slow_query_log_file`|スロークエリーログが出力される先を指定。|

### 設定の方法

- 設定値は、1 が ON、0 が OFF。
- 最後の `slow_query_log_file` のsetでエラーが出た場合は，別のディレクトリを指定するか，もしくは指定したファイルを適宜作って書き込み権限を与えること。

**MySQLのコンソールから設定**

```shell
mysql> SET GLOBAL slow_query_log = 1;
mysql> SET GLOBAL long_query_time = 5;
mysql> SET GLOBAL log_queries_not_using_indexes = 1;
mysql> SET GLOBAL slow_query_log_file = '/path/to/mysql/slow_query.log';
```

**my.cnf から設定**

```shell
[mysqld]
slow_query_log=1
long_query_time=5
log_queries_not_using_indexes=1
slow_query_log_file=/path/to/mysql/slow_query.log
```

**mysqld 再起動 (CentOS7の場合)**

```shell
systemctl restart mysqld
```

### 設定の確認

- `slow_query_log` がOFFになっているとスロークエリの出力されないため要注意。
```mysql

mysql> SHOW VARIABLES LIKE 'slow%';
+---------------------+-------------------------------+
| Variable_name       | Value                         |
+---------------------+-------------------------------+
| slow_launch_time    | 2                             |
| slow_query_log      | ON                            |
| slow_query_log_file | /path/to/mysql/slow_query.log |
+---------------------+-------------------------------+
 
 
mysql> SHOW VARIABLES LIKE 'long%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+------------+
| long_query_time | 10.000000 |
+-----------------+-----------+
```

## 集計方法

### 集計の種類

- `mysqldumpslow` コマンドでソート
- MySQL Tuner 等のツールを使用　https://github.com/major/MySQLTuner-perl

### `mysqldumpslow` コマンドの使用方法

- 発生回数でソート
```shell
mysqldumpslow -s c /path/to/mysql/slow_query.log
```
- 合計処理時間でソート
```shell
mysqldumpslow -s t /path/to/mysql/slow_query.log
```

### `mysqldumpslow` コマンドのオプション

- `-a` 匿名化されたくない場合は，`-a` オプションを利用することで生のデータを出力
- `-t` 引数に指定した個数分の結果を返却
- `-s` ソート

|オプションの引数|並べ替えができる項目|
|---|---|
|`t`|Time|
|`l`|Lock|
|`r`|Rows|
|`c`|Count|

## 実行計画

- http://nippondanji.blogspot.com/2009/03/mysqlexplain.html

### 実行計画とは

- MySQLが内部的に立てる実行手順 = MySQLの意思表示。

### 確認の構文

- `EXPLAIN` を、`SELECT`・`DELETE`・`INSERT`・`REPLACE`・`UPDATE` の先頭につけて実行。
- MySQL5.6.3より前は `SELECT` のみ使用可能なため、`UPDATE`・`DELETE` を `SELECT` に書き換えて使用。
```sql
EXPLAIN SELECT
    id,
    title
FROM
    posts
WHERE
    title LIKE "Sample%";
```

### 確認のステップ

1. `id` `select_type` `table` フィールドを見て、どのテーブルがどの順序でアクセスされるのかを知る。  
これらはクエリの構造を示すフィールドであると言える。  
サブクエリが含まれている場合には `EXPLAIN` の表示順とアクセスされる順序が異なる場合があるので気をつける必要がある。  
1. `type` `key` `ref` `rows` フィールドを見て、各テーブルから行がどのようにフェッチされるのかを知る。  
どのテーブルへのアクセスが最も重いか（クエリの性能の足を引っ張っているのか）を、これらのフィールドから判断することができる。  
1. `Extra` フィールドを見て、Optimizer（MySQLの最適化装置）がどのように判断して、各々のテーブルへのアクセスにおいて何を実行しているのかを知る。  
`Extra` フィールドは Optimizer（MySQLの最適化装置）の挙動を示すものであり、クエリの全体像を把握するのに役立つ。  

### 項目の説明

**id**

- `SELECT` ごとに振られる `ID`。
- 処理順でない場合があるため要注意。詳細は `select_type` の補足欄を参照。

**select_type**

- `SELECT` の種類。
- `SIMPLE`, `SUBQUERY`, `UNION` 等。

＜`JOIN` の場合＞

- クエリが `JOIN` だけから構成されている場合、`SIMPLE` と表示される。  
複雑な `JOIN` であっても `COMPLEX` とはならず `SIMPLE` となるため、誤解しないよう要注意。
- `EXPLAIN` の出力順序がどのテーブルから処理するかを反映している。  

＜サブクエリの場合＞  
サブクエリが絡む場合、以下の5種類のいずれかが出力される。  
サブクエリの場合は実行順序に気をつける必要がある。  
`DERIVED` の場合、サブクエリ→外部クエリの順番でクエリが実行される。  
それ以外の場合は外部クエリ→サブクエリの順番でクエリが実行される。  
ただし `SUBQUERY` の場合はサブクエリが本当に実行されるのは最初の一回だけで、それ以降はキャッシュされた実行結果が利用される。  
`DEPENDENT SUBQUERY` および `UNCACHEABLE SUBQUERY` の場合はサブクエリが行の評価の度に実行されることになる。  
サブクエリの場合、外部クエリとサブクエリでは別々のidがつけられる。  

- `PRIMARY`：外部クエリを示す。
- `SUBQUERY`：相関関係のないサブクエリ。
- `DEPENDENT SUBQUERY`：相関関係のあるサブクエリ。
- `UNCACHEABLE SUBQUERY`：実行する度に結果が変わる可能性のあるサブクエリ。
- `DERIVED`：`FROM` 句で用いられているサブクエリ。

＜`UNION` の場合＞
- `PRIMARY`：`UNION` において最初にフェッチされるテーブル。
- `UNION`：2番目以降にフェッチされるテーブル。
- `UNION RESULT`：`UNION` の実行結果。
- `DEPENDENT UNION`：`DEPENDENT SUBQUERY` が `UNIONに` なっている場合。
- `UNCACHEABLE UNION`：`UNCACHEABLE SUBQUERY` が `UNION` になっている場合。  
`<derived2>UNION` は前から順番に処理されていくだけなので、テーブルが処理される順序という観点ではわかりやすい。

**table**

- アクセスするテーブル。

**type**

- テーブルへのアクセスの種類。
- 対象のテーブルに対してどのような方法でアクセスするかを示すもの。

＜詳細＞
- `const`：`PRIMARY KEY` または `UNIQUE` インデックスのルックアップによるアクセス。最速。
- `eq_ref`：`JOIN` において `PRIARY KEY` または `UNIQUE KEY` が利用される時のアクセスタイプ。`const` と似ているが `JOIN` で用いられるところが違う。
- `ref`：ユニーク（`PRIMARY` or `UNIQUE`）ではないインデックスを使って等価検索（`WHERE key = value`）を行った時に使われるアクセスタイプ。
- `range`：インデックスを用いた範囲検索。
- `index`：`INDEX FULL SCAN`（インデックス・フル・スキャン）。インデックス全体をスキャンする必要があるのでとても遅い。
- `ALL`：`TABLE FULL SCAN`（テーブル・フル・スキャン）。インデックスがまったく利用されていないことを示す。OLTP系の処理では改善必須。  
  `<derived2>index` または `ALL` の場合は、必ずクエリのチューニングをおこなうこと。

**possible_keys**

- インデックスの候補となるキーの一覧。
- Optimizer（MySQLの最適化装置）が挙げたテーブルのアクセスに利用可能なインデックスの候補。

**key**

- 実際に選択されたキー。
- possible_keys に挙げられたインデックスの内容や統計情報を加味した上で、Optimizer（MySQLの最適化装置）によって選択されたインデックス。

**key_len**

- 選択されたキーの長さ。
- インデックスの走査は、キー長が短い方が高速である。インデックスをつけるカラムを選ぶ時にはそのことを念頭に置くこと。

**ref**

- 比較するカラム。
- 検索条件で、keyと比較されている値やカラムの種類。

＜詳細＞
- 定数が指定されている場合は const と表示される。（`WHERE foo = 1` のような場合）
- JOINが実行されている時には、結合する相手側のテーブルで検索条件として利用されているカラムが表示される。

**rows**

- スキャンする見積もり行数。
- あくまでもテーブル全体の行数やインデックスの分散具合から導き出された大まかな見積もりなので、実際にフェッチされる正確な行数ではないので要注意。
- JOINやサブクエリが関係する場合は「外部表の rows x 内部表の rows」がスキャンする行になる。  
  http://nippondanji.blogspot.com/2009/03/mysqlexplain.html

**Extra**

- Optimizer（MySQLの最適化装置）がどのような戦略を選択したか。  
 「optimizer の独り言」

＜詳細＞

- `Using where`：頻繁に出力される追加情報である。`WHERE` 句に検索条件が指定されており、なおかつインデックスを見ただけではWHERE句の条件を全て適用することが出来ない場合に表示される。
- `Using index`：クエリがインデックスだけを用いて解決できることを示す。`Covering Index` を利用している場合などに表示される。
- `Using filesort`：ソートに必要な領域がメモリ上に乗り切らずに物理ファイルに書き出しソートを行う。
- `Using temporary`：JOINの結果をソートしたり、`DISTINCT` による重複の排除を行う場合など、クエリの実行にテンポラリテーブルが必要なことを示す。
- `Using index for group-by`：`MIN` `MAX` が `GROUP BY` 句と併用されているとき、クエリがインデックスだけを用いて解決できることを示す。
- `Range checked for each record (index map: N)`：`JOIN` において `range` または `index_merge` が利用される場合に表示される。
- `Not exists`：`LEFT JOIN` において、左側のテーブルからフェッチされた行にマッチする行が右側のテーブルに存在しない場合、右側のテーブルは `NULL` となるが、右側のテーブルがNOT NULLとして定義されたフィールドで `JOIN` されている場合にはマッチしない行を探せば良い：ということを示す。
- `Using filesort` と `Using temporaty` が表示されたら、必ずチューニングすること。
- `Using filesort` とは？
  - http://nippondanji.blogspot.com/2009/03/using-filesort.html
  - クエリにORDER BYが含まれる場合、MySQLはある程度の大きさまでは全てメモリ内でクイックソートを処理する。
  - ある程度の大きさとはsort_buffer_sizeであり、これはセッションごとに変更可能である。
  - ソートに必要なメモリがsort_buffer_sizeより大きくなると、テンポラリファイル（テンポラリテーブルではない）が作成され、メモリとファイルを併用してクイックソートが実行される。


## クエリの改善策

### レコード数を絞れる条件は早い段階で記述

{{< hint danger>}}
負債は早く返さないと、あとでツケを払うことになる。
{{</hint>}}

- WHERE句の条件で対象レコード数が少なくなる条件を先に記述。
- JOINの条件は結合前の絞り込み、WHEREは結合後の絞り込みのため、JOINに抽出条件を記述したほうが効率が良い。と以前教わった。

### サブクエリを引数にとる場合、IN句よりもEXISTSを使用

例）授業Aと授業Bを受講している生徒  

改善前）
```sql {hl_lines=[6],linenostart=1}
SELECT
    *
FROM
    class_a
WHERE
    student_id IN(SELECT student_id FROM class_b);
```

改善後）
```sql {hl_lines=[6],linenostart=1}
SELECT
    *
FROM
    class_a a
WHERE
    EXISTS(SELECT * FROM class_b b WHERE a.student_id = b.student_id);
```

#### EXISTSのほうが速いと期待できる理由

- 結合キー（この場合 student_id）にインデックスが張られている場合、class_b の実表は見に行かず、インデックスを参照するのみで済む。
- EXISTSは1行でも条件に合致する行を見つけたら、そこで検索を打ち切るので、INのように全件検索の必要がない。NOT EXISTS の場合も同様。

### 中間テーブルを減らす

#### HAVING句を活用

改善前）無駄な中間テーブルを使用
```sql {hl_lines=[4,7,12,14],linenostart=1}
SELECT
    *
FROM
    (
        SELECT
            sale_date,
            MAX(quantity) AS max_quantity
        FROM
            sales_histories
        GROUP BY
            sale_date
    ) AS tmp
WHERE
    max_quantity >= 10;
```

改善後）HAVING句を活用
```sql {hl_lines=[3,8,9],linenostart=1}
SELECT
    sale_date,
    MAX(quantity) AS max_quantity
FROM
    sales_histories
GROUP BY
    sale_date
HAVING
    max_quantity >= 10;
```

#### INで複数のキーを利用する場合は1箇所にまとめる

改善前）
```sql {hl_lines=[8,9],linenostart=1}
SELECT
    id,
    state,
    city
FROM
    addresses1 a1
WHERE
    a1.state IN (SELECT a2.state FROM address2 a2 WHERE a1.id = a2.id)
    AND a1.city IN(SELECT a2.city FROM address2 a2 WHERE a1.id = a2.id);
```

改善後）
```sql {hl_lines=[6],linenostart=1}
SELECT
    *
FROM
    addresses1 a1
WHERE
    (id, state, city) IN (SELECT id, state, city FROM address2 a2);
```

### ソートを回避

#### ソートが発生する代表的な演算

- `GROUP BY`
- `ORDER BY`
- `DISTINCT`
- 集約関数：`SUM`、`COUNT`、`AVG`、`MAX`、`MIN` etc
- 集合演算子：`UNION`、`INTERSECT`、`EXCEPT` etc
- ウィンドウ関数：`RANK`、`ROW_NUMBER` etc （MySQL8以降）

#### DISTINCTをEXISTSで代用

結合に劣らず高速に動作  

改善前）
```sql {hl_lines=[2],linenostart=1}
SELECT
    DISTINCT(i.item_no)
FROM
    items i
    INNER JOIN sales_histories s ON i.item_no = s.item_no;
```

改善後）
```sql {hl_lines=[6],linenostart=1}
SELECT
    item_no
FROM
    items i
WHERE
    EXISTS(SELECT s.id FROM sales_histories s WHERE i.item_no = s.item_no);
```

### WHERE句で書ける条件はHAVING句には書かない

改善前）集約した後にHAVING句でフィルタリング
```sql {hl_lines=[8,9],linenostart=1}
SELECT
    sale_date,
    SUM(quantity)
FROM
    sales_histories
GROUP BY
    sale_date
HAVING
    sale_date = '2020-01-01';
```

改善後）集約する前にWHERE句でフィルタリング
```sql {hl_lines=[6,7],linenostart=1}
SELECT
    sale_date,
    SUM(quantity)
FROM
    sales_histories
WHERE
    sale_date = '2020-01-01'
GROUP BY
    sale_date;
```

HAVING句は、集約した後のビューに対する条件を設定するが、残念なことに集約後のビューは元テーブルのインデックスまでは引き継がないケースが多い。

### GROUPBY句とORDERBY句でインデックスを使用する

- GROUP BY句やORDER BY句は、通常 並べ替えのためのソートをおこなう。
- インデックスの存在する列をキーに指定すること。

極値関数（MAX/MIN）でインデックスを使用する  
例）item_no インデックスなし、item_id インデックス あり  

改善前）全件検索が必要
```sql
SELECT MAX(item_no) FROM items;
```

改善後）インデックスのスキャンだけ済ませ実表への検索を回避
```sql
SELECT MAX(item_id) FROM items;
```

## インデックス

### インデックスの操作

インデックスの確認
```sql
SHOW INDEX FROM テーブル名;
SHOW INDEX FROM sample_table;
```

インデックスの追加
```sql
ALTER TABLE テーブル名 ADD INDEX インデックス名(カラム名);
ALTER TABLE posts ADD INDEX index_posts_on_updated_at(updated_at);
```

複合インデックスの追加
```sql
ALTER TABLE posts ADD INDEX index_posts_on_created_at_updated_at(created_at, updated_at);
```

インデックスを削除
```sql
ALTER TABLE テーブル名 DROP INDEX インデックス名;
ALTER TABLE posts DROP INDEX index_posts_on_updated_at;
```

### インデックスを追加する際のポイント

- カーディナリティの高いカラムを選択。
- 複合インデックスのカラム順に注意。指定順が重要。（country_id, city_id）
- 更新性能・キャッシュ効率が低下するため必要なものにだけ貼ること。

### カーディナリティとは

- インデックス内のユニークな値の多さを表した指標。
  - 高い例：AUTO_INCREMENT, 住所, 更新日時
  - 低い例：フラグ, 性別, カテゴリー, 都道府県
- カーディナリティが低いと効果が少ない。絞り込めない可能性が高い。
- また、インデックスを経由することによるオーバーヘッドが無視できない。
- 分布が偏っていれば効果が大きくなる可能性がある、条件によって効果が異なる。

### インデックスが適用されないケース

- `Extra` 欄が `Using where` の場合 かつ インデックスが使われていない場合、対象テーブルの行を1つ1つスキャンして探していることになる。

**NULL述語を指定している場合**  

```sql
WHERE indexed_column IS NULL
```

IS NOT NULLの代用案
```sql
WHERE indexed_column > 0
```

- 原理：最小値より小さい数を指定して不等号を使えば、`indexed_column` のすべての値が選択される。
- NULLの行だけ `indexed_column > NULL` が `unknown` に評価され抽出の対象外となる。
- このようなトリッキーな記述は読み手を混乱させる可能性があるため、どうしても必要な場合のみ使用すること。

**左辺で関数を使用している場合**  

例）文字列の処理  
改善前）左辺で関数を使用しているためインデックスが適用されないケース
```sql
-- NG
WHERE SUBSTRING(indexed_column, 1, 3) = 'abc'
```

改善後）関数を使用せず別な方法で代用
```sql
-- OK
WHERE indexed_column LIKE 'abc%'
```

例）暗号化/復号化  
改善前）左辺で関数を使用しているためインデックスが適用されないケース
```sql
-- NG
WHERE AES_DECRYPT(indexed_column, 'Encryption Key') = 'foo@bar.com'
```

改善後）右辺で暗号化し完全一致するためインデックスが適用されるケース
```sql
-- OK
WHERE indexed_column = AES_ENCRYPT('foo@bar.com', 'Encryption Key')
```

**演算処理を使用している場合**  

改善前）左辺に演算処理が含まれておりインデックスが適用されないケース
```sql
-- NG
WHERE indexed_column * 1.1 > 100
```

改善策）インデックスを貼る場合「左辺は裸」が基本
```sql
-- OK
WHERE indexed_column > 100 / 1.1
```

**否定形での条件指定している場合**  

`<>`, `!=`, `NOT IN`
```sql
-- NG
WHERE indexed_column <> 'abc'
```

**ORでの条件指定している場合**  

改善前）
```sql
-- NG
WHERE indexed_column = 'abc' OR indexed_column = 'def'
```

改善後）
```sql
-- OK
WHERE indexed_column IN ('abc', 'def')
```

**中間一致・後方一致でのLIKE述語を使用している場合**  

```sql
-- NG
WHERE indexed_column LIKE '%abc%'
```

Note: 前方一致の場合は インデックスが適用される

**複合インデックスが貼ってあるが、WHERE句の列の順番誤りの場合**  

質問）`(col_1, col_2, col_3)` の順番で複合インデックスが貼られている場合  
以下の①〜④の中でインデックスが適用されるものはどれ？
```sql
-- ①
SELECT * FROM sample_table WHERE col_1 = 10 AND col_2 = 100 AND col_3 = 500;
-- ②
SELECT * FROM sample_table WHERE col_1 = 10 AND col_2 = 100;
-- ③
SELECT * FROM sample_table WHERE　col_1 = 10 AND col_3 = 500;
-- ④
SELECT * FROM sample_table WHERE　col_2 = 100 AND col_3 = 500;
```

{{< expand "答え" "..." >}}
① OK  
② OK  
③ NG  
④ NG  
{{< /expand >}}

**暗黙の型変換している場合**  

カラム `col_1` が文字列型の場合、インデックスが適用ものはどれ？
```sql
-- ①
SELECT * FROM sample_table WHERE col_1 = 10;
-- ②
SELECT * FROM sample_table WHERE col_1 = '10';
-- ③
SELECT * FROM sample_table WHERE col_1 = CAST(10 AS CHAR);
```

{{< expand "答え" "..." >}}
① NG  
② OK  
③ OK ※ CAST(10, AS CHAR(2)) によって文字列として扱われるため。  
{{< /expand >}}

暗黙の型変換は、オーバーヘッドを発生させるだけでなく、インデックスまで使用不可になる。  
MySQLではエラーにならないが、PostgreSQLの場合はエラーになる。

**小さなテーブルの場合、または、テーブルの大半の行が対象の場合、インデックスを処理する方が遅くなるケース**  

> 小さなテーブルまたは、レポートクエリーが行の大半またはすべてを処理する大きなテーブルに対するクエリーでは、インデックスはあまり重要ではありません。クエリーで行の大半にアクセスする必要がある場合は、順次読み取る方が、インデックスを処理するより高速です。クエリーですべての行が必要でない場合でも、順次読み取りは、ディスクシークを最小にします。

引用：[8.3.1 MySQL のインデックスの使用の仕組み](https://dev.mysql.com/doc/refman/5.6/ja/mysql-indexes.html)

## その他

### `INDEX FULL SCAN` (全索引検索)とは

- 複合インデックスのうち1つしか効いていない場合、ツリー構造の複合インデックスの組み合わせを すべてスキャンする状態のこと。

例）
- 複合インデックス　`(col_1, col_2)`
- `WHERE` 句で処理する時点で `col_2` が順番誤りのためインデックスの適用外。
- その後処理される `ORDER BY` 句の `col_1` のみインデックスが適用。
- 結果的に複合インデックスのすべてのスキャンが必要。
```sql
SELECT col_1, col_2, col_3 FROM sample_table WHERE col_2 = 10 ORDER BY col_1;
```

## 参照

- [MySQL 公式ドキュメント](https://dev.mysql.com/doc/refman/5.6/ja/explain.html)
- [達人に学ぶSQL徹底指南書 第2版](https://books.rakuten.co.jp/rb/15596931/)
- [SQL実践入門](https://books.rakuten.co.jp/rb/13190627/)
- [MySQL勉強会 Yahoo](https://www.slideshare.net/techblogyahoo/mysql-58540246)
- [MySQL勉強会](https://www.slideshare.net/microad_engineer/my-sql-22426126)
- [MySQLのEXPLAINを徹底開設](http://nippondanji.blogspot.com/2009/03/mysqlexplain.html)
- [Using filesort](http://nippondanji.blogspot.com/2009/03/using-filesort.html)
- [スロークエリログ](https://gihyo.jp/dev/serial/01/mysql-road-construction-news/0007)
- [MySQL Tuner](https://github.com/major/MySQLTuner-perl)
- [津島博士のパフォーマンス講座　 第21回 索引について（２）](https://sh2.hatenablog.jp/entry/20111217)
- [MySQLのIndexをはるコツ](https://qiita.com/katsukii/items/3409e3c3c96580d37c2b)
- [MySQLでAES_ENCRYPTとAES_DECRYPTでvarbinary型のINDEX動作](https://qiita.com/kichiweb/items/e7de801f972d9b41ef01)
- [DBのインデックスと複合インデックス](https://qiita.com/towtow/items/4089dad004b7c25985e3)
