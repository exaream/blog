---
date: 2020-01-01
lastmod: ["lastmod"]
title: MySQL
tags: ["SQL", "MySQL"]
---

# MySQL

## Preparation

### Install to macOS
```bash
$ brew install mysql
```
### Start
```bash
$ mysql.server start
Starting MySQL
....... SUCCESS!
```
### Check the vertion
```bash
$ mysql --version
mysql  Ver 8.0.26 for macos11.3 on x86_64 (Homebrew)
```

### Make initial settings
```bash
$ mysql_secure_installation
```
* パスワード強度検証プラグインの利用有無
* パスワード強度の選択
* root ユーザのパスワード設定
* 匿名ユーザの削除
* リモートからの root ユーザとしてのログインの禁止
* サンプル用 test データベースの削除

```bash {hl_lines=[1,11,12,19,20,24,26,30,37,38,45,46,54,55,64,65]}
$ mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?
### パスワード強度検証プラグインの利用
Press y|Y for Yes, any other key for No: y ### y と入力

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary file
### パスワード検証ポリシーの選択
Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 2　### 2 と入力

Please set the password for root here.

New password: ### rootユーザー用のパスワードを入力

Re-enter new password: ### rootユーザー用のパスワードを再入力

Estimated strength of the password: 100
Do you wish to continue with the password provided?
(Press y|Y for Yes, any other key for No) : y　### y と入力
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.
### 匿名ユーザーの削除
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y ### y と入力
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.
### リモートからの root ログインの拒否
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y ### y と入力
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.

### test データベースの削除
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y　### y と入力
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
### 特権テーブルの即時適用
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y　### y と入力
Success.

All done! 
```

## ログイン

e.g) rootユーザーでのログイン
```bash
$ mysql -uroot -p
Enter password: ### 設定したパスワードを入力
```

## MySQLの情報

### バージョン
```mysql
SELECT VERSION();
```

### タイムゾーン
```mysql
SHOW VARIABLES LIKE '%time_zone%';
```

### 文字コード
```mysql
SHOW VARIABLES LIKE '%character\_set\_%';
```
```mysql
SHOW VARIABLES LIKE 'collation%';
```

## データベース/テーブル/カラムの一覧

### データベースの一覧
```mysql
SHOW DATABASES;
```
```mysql
SELECT
    DISTINCT table_schema AS database_name
FROM
    information_schema.tables
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
ORDER BY
    table_schema;
```

### テーブルの一覧

```mysql
USE sample_database;
SHOW TABLES;
```
```mysql
SHOW TABLES FROM sample_database;
```
```mysql
SELECT
    table_schema AS database_name,
    table_name
FROM
    information_schema.tables
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
    AND table_type = 'BASE TABLE'
ORDER BY
    table_schema,
    table_type,
    table_name;
```

### テーブルごとの自動採番 AUTO_INCREMENT の一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    auto_increment
FROM
    information_schema.tables
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
    AND table_type = 'BASE TABLE'
ORDER BY
    table_schema,
    table_name;
```

### テーブルごとのカラムの一覧（カンマ区切り）
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    GROUP_CONCAT(column_name) AS column_names
FROM
    information_schema.columns
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
GROUP BY
    table_schema,
    table_name
```

### テーブルの物理名と論理名（テーブル・コメント）の一覧
```mysql
SELECT
    table_schema AS databae_name,
    table_name,
    table_comment
FROM
    information_schema.tables
WHERE
    table_schema = 'sample_database'
ORDER BY
    table_schema,
    table_name;
```

### カラムの物理名と論理名（カラム・コメント）の一覧
```mysql
SELECT
    table_schema AS databae_name,
    table_name,
    column_name,
    column_comment
FROM
    information_schema.columns
ORDER BY
    table_schema,
    table_name; 
```

### 特定のカラムを含むテーブルの一覧
```mysql
SELECT
    table_schema AS databae_name,
    table_name,
    column_name,
    column_comment
FROM
    information_schema.columns
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
    AND column_name LIKE '%sample_column%'
ORDER BY
    table_schema,
    table_name,
    column_name;
```

### ビューの一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name AS view_name
FROM
    information_schema.tables
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
    AND table_type = 'VIEW'
ORDER BY
    table_schema,
    table_name;
```

## テーブルのキー情報の一覧

### テーブルごとのプライマリーキー制約の一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    column_name AS primary_key
FROM
    information_schema.KEY_COLUMN_USAGE
WHERE
    constraint_name = 'PRIMARY';
```

### テーブルごとのユニークキー制約の一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    GROUP_CONCAT(column_name) AS unique_keys
FROM
    information_schema.KEY_COLUMN_USAGE
WHERE
    position_in_unique_constraint = 1
GROUP BY
    table_schema,
    table_name;
```

### テーブルごとの外部キー制約の一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    GROUP_CONCAT(
        CONCAT(
            column_name,
            '=',
            referenced_table_name,
            '.',
            referenced_column_name
        )
    ) AS referenced
FROM
    information_schema.KEY_COLUMN_USAGE
WHERE
    referenced_table_name IS NOT NULL
GROUP BY
    table_schema,
    table_name;
```

### テーブルごとのプライマリーキー・ユニークキー・外部キー一覧
```mysql
SELECT
    table_info.*,
    primary_info.primary_key,
    unique_info.unique_keys,
    reference_info.referenced
FROM
    (
        SELECT
            table_schema AS database_name,
            table_name
        FROM
            information_schema.tables
        WHERE
            table_type = 'BASE TABLE'
    ) AS table_info
    LEFT JOIN (
        SELECT
            table_schema AS database_name,
            table_name,
            GROUP_CONCAT(column_name) AS unique_keys
        FROM
            information_schema.KEY_COLUMN_USAGE
        WHERE
            position_in_unique_constraint = 1
        GROUP BY
            table_schema,
            table_name
    ) AS unique_info ON table_info.database_name = unique_info.database_name
    AND table_info.table_name = unique_info.table_name
    LEFT JOIN (
        SELECT
            table_schema AS database_name,
            table_name,
            column_name AS primary_key
        FROM
            information_schema.KEY_COLUMN_USAGE
        WHERE
            constraint_name = 'PRIMARY'
    ) AS primary_info ON table_info.database_name = primary_info.database_name
    AND table_info.table_name = primary_info.table_name
    LEFT JOIN (
        SELECT
            table_schema AS database_name,
            table_name,
            GROUP_CONCAT(
                CONCAT(
                    column_name,
                    '=',
                    referenced_table_name,
                    '.',
                    referenced_column_name
                )
            ) AS referenced
        FROM
            information_schema.KEY_COLUMN_USAGE
        WHERE
            referenced_table_name IS NOT NULL
        GROUP BY
            table_schema,
            table_name
    ) AS reference_info ON table_info.database_name = reference_info.database_name
    AND table_info.table_name = reference_info.table_name
```

## データ容量の一覧

### データベースごとのサイズの一覧
```mysql
SELECT
    table_schema AS database_name,
    CONCAT(
        SUM(data_length + index_length) / (1024 * 1024),
        ' MB'
    ) AS db_size
FROM
    information_schema.tables
GROUP BY
    table_schema
ORDER BY
    table_schema;
```

### データベースごとのテーブル数の一覧
```mysql
SELECT
    table_schema AS database_name,
    COUNT(*) AS table_count
FROM
    information_schema.tables
WHERE
    table_type = 'BASE TABLE'
GROUP BY
    table_schema;
```

### テーブルごとのサイズの一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    CONCAT(
        (data_length + index_length) / (1024 * 1024),
        ' MB'
    ) AS table_size
FROM
    information_schema.tables
WHERE
    table_type = 'BASE TABLE'
    AND table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
ORDER BY
    table_schema,
    (data_length + index_length) DESC,
    table_name;
```

### テーブルごとのレコード数の一覧
```mysql
SELECT
    table_schema AS database_name,
    table_name,
    table_rows
FROM
    information_schema.tables AS `target`
WHERE
    table_type = 'BASE TABLE'
    AND table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
ORDER BY
    table_schema,
    table_rows DESC,
    table_name;
```

### データベース/テーブルごとの件数/容量/カラム/キー情報/文字コード/AUTO_INCREMENTの一覧
```mysql
SELECT
    table_info.table_schema AS database_name,
    database_info.table_count,
    database_info.db_size,
    table_info.table_name,
    table_info.table_rows,
    table_info.table_size,
    columns_info.column_names,
    key_info.primary_key,
    key_info.unique_keys,
    key_info.referenced,
    table_info.auto_increment,
    table_info.table_collation AS character_info
FROM
    (
        -- 全テーブル情報
        SELECT
            *,
            CONCAT(
                (data_length + index_length) / (1024 * 1024),
                ' MB'
            ) AS table_size
        FROM
            information_schema.tables
        WHERE
            table_schema NOT IN (
                'mysql',
                'perfomance_schema',
                'information_schema'
            )
            AND table_type = 'BASE TABLE'
    ) AS table_info
    LEFT JOIN (
        -- データベースの容量とテーブル数
        SELECT
            table_schema AS database_name,
            CONCAT(
                SUM(data_length + index_length) / (1024 * 1024),
                ' MB'
            ) AS db_size,
            SUM(
                CASE
                    WHEN table_type = 'BASE TABLE' THEN 1
                    ELSE 0
                END
            ) AS table_count
        FROM
            information_schema.tables
        GROUP BY
            table_schema
    ) AS database_info ON table_info.table_schema = database_info.database_name
    LEFT JOIN (
        -- テーブルごとのカラム
        SELECT
            table_schema AS database_name,
            table_name,
            GROUP_CONCAT(column_name) AS column_names
        FROM
            information_schema.columns
        WHERE
            table_schema NOT IN (
                'mysql',
                'perfomance_schema',
                'information_schema'
            )
        GROUP BY
            table_schema,
            table_name
    ) AS columns_info ON table_info.table_schema = columns_info.database_name
    AND table_info.table_name = columns_info.table_name
    LEFT JOIN (
        -- テーブルのキー情報（プライマリーキー、ユニークキー、外部キー）
        SELECT
            table_info.*,
            primary_info.primary_key,
            unique_info.unique_keys,
            reference_info.referenced
        FROM
            (
                SELECT
                    table_schema AS database_name,
                    table_name
                FROM
                    information_schema.tables
                WHERE
                    table_type = 'BASE TABLE'
            ) AS table_info
            LEFT JOIN (
                SELECT
                    table_schema AS database_name,
                    table_name,
                    GROUP_CONCAT(column_name) AS unique_keys
                FROM
                    information_schema.KEY_COLUMN_USAGE
                WHERE
                    position_in_unique_constraint = 1
                GROUP BY
                    table_schema,
                    table_name
            ) AS unique_info ON table_info.database_name = unique_info.database_name
            AND table_info.table_name = unique_info.table_name
            LEFT JOIN (
                SELECT
                    table_schema AS database_name,
                    table_name,
                    column_name AS primary_key
                FROM
                    information_schema.KEY_COLUMN_USAGE
                WHERE
                    constraint_name = 'PRIMARY'
            ) AS primary_info ON table_info.database_name = primary_info.database_name
            AND table_info.table_name = primary_info.table_name
            LEFT JOIN (
                SELECT
                    table_schema AS database_name,
                    table_name,
                    GROUP_CONCAT(
                        CONCAT(
                            column_name,
                            '=',
                            referenced_table_name,
                            '.',
                            referenced_column_name
                        )
                    ) AS referenced
                FROM
                    information_schema.KEY_COLUMN_USAGE
                WHERE
                    referenced_table_name IS NOT NULL
                GROUP BY
                    table_schema,
                    table_name
            ) AS reference_info ON table_info.database_name = reference_info.database_name
            AND table_info.table_name = reference_info.table_name
    ) AS key_info ON table_info.table_schema = key_info.database_name
    AND table_info.table_name = key_info.table_name
ORDER BY
    table_info.table_schema,
    table_info.table_name;
```

## データベース
### データベースの新規作成
{{<hint info>}}
* CHARACTER SET 文字コード
  * utf8mb4 絵文字にも対応。
* COLLATE 照合順序  
  * utf8mb4_bin: 全ての区別あり（大文字小文字/全角半角の区別あり）
  * utf8mb4_general_ci: 大文字小文字の区別なし
  * utf8mb4_unicode_ci: 大文字小文字/全角半角の区別なし
{{</hint>}}

```mysql
CREATE DATABASE sample_db DEFAULT CHARACTER SET = utf8mb4 DEFAULT COLLATE = utf8mb4_bin;
```

### データベースの文字コードの変更
```mysql
ALTER DATABASE sample_db DEFAULT CHARACTER SET = utf8mb4;
```


## ユーザー

### ユーザーの新規作成
```mysql
CREATE USER 'sample_user'@'localhost' IDENTIFIED BY 'sample_password';
```


### ユーザーのパスワードの設定

#### ログイン中ユーザーのパスワードの設定
```mysql
-- ログイン中のユーザーを確認
SELECT CURRENT_USER();
-- パスワードを設定
SET PASSWORD = PASSWORD('sample_password');
```

#### 特定のユーザーのパスワードの設定
```mysql
SET PASSWORD FOR 'sample_user'@'localhost' = PASSWORD('sample_password');
```

### ユーザーの権限の設定

#### データベース権限
sample_user に sample_db の全テーブルの全ての権限を付与
```mysql
GRANT ALL ON sample_db.* TO 'sample_user'@'localhost';
```
sample_user に sample_db の全テーブルの参照/新規登録の権限を付与
```mysql
GRANT SELECT, INSERT ON sample_db.* TO 'sample_user'@'localhost';
```

#### テーブル権限

sample_user に sample_db の sample_table の参照権限を付与
```mysql
GRANT SELECT ON sample_db.sample_table TO 'sample_user'@'localhost';
```

sample_user の既存の権限を変更せずに 1時間あたりの発行可能クエリ数を制限
{{<hint info>}}
* MAX_QUERIES_PER_HOUR count  クエリ数
* MAX_UPDATES_PER_HOUR count  更新件数
* MAX_CONNECTIONS_PER_HOUR count  コネクション数（同時接続の最大数を制限）

(結果がクエリーキャッシュから得られたクエリーは、MAX_QUERIES_PER_HOUR 制限に対してカウントされません。) count が 0 (デフォルト) である場合、これは、このアカウントに対する制限が存在しないことを示します。
{{< /hint >}}
```mysql
GRANT USAGE ON *.* TO 'sample_user'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
```

#### カラム権限
カラム権限で付与される権限 INSERT, SELECT, UPDATE の後にカッコで囲まれた 1つ以上のカラムを指定 
```mysql
GRANT SELECT (sample_column_1, sample_column_2), UPDATE (sample_column_1) ON sample_db.sample_table TO 'sample_user'@'localhost';
```




## 権限

### ユーザーごとの権限の一覧
```mysql
SELECT
    grantee AS user,
    is_grantable AS is_grantable,
    GROUP_CONCAT(privilege_type) AS privileges
FROM
    information_schema.user_privileges
GROUP BY
    grantee,
    is_grantable
ORDER BY
    grantee;
```

### データベースごとの権限の一覧
```mysql
SELECT
    grantee AS user,
    table_schema AS db,
    GROUP_CONCAT(privilege_type) AS privileges
FROM
    information_schema.schema_privileges
GROUP BY
    grantee,
    table_schema
ORDER BY
    grantee,
    table_schema;
```

### テーブルごとの権限の一覧
```mysql
SELECT
    grantee AS user,
    table_schema AS db,
    table_name AS `table`,
    GROUP_CONCAT(privilege_type) AS privileges
FROM
    information_schema.table_privileges
GROUP BY
    grantee,
    table_schema,
    table_name
ORDER BY
    grantee,
    table_schema,
    table_name;
```

### カラムごとの権限の一覧
```mysql
SELECT
    grantee AS user,
    table_schema AS db,
    table_name AS `table`,
    column_name AS `column`,
    GROUP_CONCAT(privilege_type) AS privileges
FROM
    information_schema.column_privileges
GROUP BY
    grantee,
    table_schema,
    table_name,
    column_name
ORDER BY
    grantee,
    table_schema,
    table_name,
    column_name;
```

### ユーザーごと・データベースごと・テーブルごと・カラムごとの権限の一覧
```mysql
SELECT
    *
FROM
    (
        SELECT
            grantee AS user,
            is_grantable AS is_grantable,
            '-' AS db,
            '-' AS `table`,
            '-' AS `column`,
            GROUP_CONCAT(privilege_type) AS privileges
        FROM
            information_schema.user_privileges
        GROUP BY
            grantee,
            is_grantable
        UNION
        ALL
        SELECT
            grantee AS user,
            is_grantable AS is_grantable,
            table_schema AS db,
            '-' AS `table`,
            '-' AS `column`,
            GROUP_CONCAT(privilege_type) AS privileges
        FROM
            information_schema.schema_privileges
        GROUP BY
            grantee,
            is_grantable,
            table_schema
        UNION
        ALL
        SELECT
            grantee AS user,
            is_grantable AS is_grantable,
            table_schema AS db,
            table_name AS `table`,
            '-' AS `column`,
            GROUP_CONCAT(privilege_type) AS privileges
        FROM
            information_schema.table_privileges
        GROUP BY
            grantee,
            is_grantable,
            table_schema,
            table_name
        UNION
        ALL
        SELECT
            grantee AS user,
            is_grantable AS is_grantable,
            table_schema AS db,
            table_name AS `table`,
            column_name AS `column`,
            GROUP_CONCAT(privilege_type) AS privileges
        FROM
            information_schema.column_privileges
        GROUP BY
            grantee,
            is_grantable,
            table_schema,
            table_name,
            column_name
    ) AS authority
ORDER BY
    user,
    db,
    `table`,
    `column`;
```

## 日付・時刻

[MySQL Reference Manual - Date and Time Functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html)

### 現在時刻
```mysql
SELECT NOW(); -- 2020-01-05 13:30:10
```

### 現在時刻の時刻のみ
```mysql
SELECT CURRENT_TIME(); -- 13:30:10
```

### 今日
```mysql
SELECT CURRENT_DATE(); -- 2020-01-05
```

### 昨日
```mysql
SELECT CURRENT_DATE() - INTERVAL 1 DAY; -- 2020-01-04
```

### 明日
```mysql
SELECT CURRENT_DATE() + INTERVAL 1 DAY; -- 2020-01-06
```

### 1週間前
```mysql
SELECT CURRENT_DATE() - INTERVAL 7 DAY; -- 2019-12-29
```

### 1週間後
```mysql
SELECT CURRENT_DATE() + INTERVAL 7 DAY; -- 2020-01-12
```

### 当月1日
```mysql
SELECT DATE_FORMAT(CURRENT_DATE(), '%Y-%m-01'); -- 2020-01-01
```

### 当月末日
```mysql
SELECT LAST_DAY(CURRENT_DATE()); -- 2020-01-31
```

### 当月1日〜当月末日
```mysql {hl_lines=["6-7"]}
SELECT
    *
FROM
    sample_table
WHERE
    sample_column >= DATE_FORMAT(CURRENT_DATE(), '%Y-%m-01')
    AND sample_column <= LAST_DAY(CURRENT_DATE());
```

### 先月1日
```mysql
SELECT DATE_FORMAT(DATE_SUB(CURRENT_DATE(), INTERVAL 1 MONTH), '%Y-%m-01'); -- 2019-12-01
-- or
SELECT DATE_FORMAT(DATE_ADD(CURRENT_DATE(), INTERVAL -1 MONTH), '%Y-%m-01'); -- 2019-12-01
```

### 先月末日
```mysql
SELECT LAST_DAY(DATE_SUB(CURRENT_DATE(), INTERVAL 1 MONTH)); -- 2019-12-31
-- or
SELECT LAST_DAY(DATE_ADD(CURRENT_DATE(), INTERVAL -1 MONTH)); -- 2019-12-31
```

### 来月1日
```mysql
SELECT DATE_FORMAT(DATE_ADD(CURRENT_DATE(), INTERVAL 1 MONTH), '%Y-%m-01'); -- 2020-02-01
```

### 来月末日
```mysql
SELECT LAST_DAY(DATE_ADD(CURRENT_DATE(), INTERVAL 1 MONTH)); -- 2020-02-29
```

### 昨年
```mysql
SELECT DATE_FORMAT((NOW() - INTERVAL 1 YEAR), '%Y-%m-%d');
```
### 来年
```mysql
SELECT DATE_FORMAT((NOW() + INTERVAL 1 YEAR), '%Y-%m-%d');
```

### 3年前
```mysql
SELECT DATE_FORMAT((NOW() - INTERVAL 3 YEAR), '%Y-%m-%d');
```
### 3年後
```mysql
SELECT DATE_FORMAT((NOW() + INTERVAL 3 YEAR), '%Y-%m-%d');
```

### 日付の差分
```mysql
SELECT DATEDIFF('2020-12-31 23:59:59','2020-12-30'); -- 1
SELECT DATEDIFF('2020-11-30 23:59:59','2020-12-31'); -- -31
```
### 年のみ
```mysql
SELECT YEAR('2020-12-31'); -- 2020
```

### 月のみ
```mysql
SELECT MONTH('2020-12-31'); -- 12
-- or
SELECT DAYOFMONTH('2020-12-31'); -- 12
```

### 日のみ
```mysql
SELECT DAY('2020-12-31'); -- 31
-- or
SELECT DAYOFMONTH('2020-12-31'); -- 31
```

### 曜日
曜日インデックス (1 = Sunday、2 = Monday、…、7 = Saturday) 
```mysql
SELECT DAYOFWEEK('2007-02-03'); -- 7
```
曜日インデックス (0 = Monday、1 = Tuesday、…6 = Sunday) 
```mysql
SELECT WEEKDAY('2008-02-03 22:23:00'); -- 6
```

### 曜日（日本語）
```mysql
SELECT
    CASE
        DATE_FORMAT(NOW(), '%w')
        WHEN 0 THEN '日'
        WHEN 1 THEN '月'
        WHEN 2 THEN '火'
        WHEN 3 THEN '水'
        WHEN 4 THEN '木'
        WHEN 5 THEN '金'
        WHEN 6 THEN '土'
    END;
```

### 年内の歴週
```mysql
SELECT WEEKOFYEAR('2008-02-20'); -- 8
```
### 年内の通日
```mysql
SELECT DAYOFYEAR('2007-02-03'); -- 34
```

## 便利関数

### 値がNULLの場合の初期値を返却 IFNULL
```mysql {hl_lines=[2],linenostart=1}
SELECT
    IFNULL(name, 'anonym') AS name
FROM
    sample_table;
```

### 最初のNULL以外の値を返却 COALESCE
NULL以外の値がない場合 NULLを返却
```mysql {hl_lines=["2-7"],linenostart=1}
SELECT
    COALESCE(
        phone_number_smart_private,
        phone_number_home,
        phone_number_office,
        NULL
    ) AS phone_number
FROM
    sample_table;
```

### カンマ区切りで結合 GROUP_CONCAT
```mysql {hl_lines=[2],linenostart=1}
SELECT
    GROUP_CONCAT(id) AS id -- '1,2,3,4,5'
FROM
    sample_table
ORDER BY
    id;
```

### カンマ区切りの値を検索 FIND_IN_SET
{{< hint danger >}}
INDEXが効かないため要注意
{{</hint>}}
```mysql {hl_lines=[6],linenostart=1}
SELECT
    *
FROM
    sample_table
WHERE
    FIND_IN_SET(id, '1,2,3,4');
```
### 指定した順番に並べ替え FIELD
```mysql {hl_lines=[8],linenostart=1}
SELECT
    *
FROM
    sample_table
WHERE
    id IN(1, 2, 3)
ORDER BY
    FIELD(id, 3, 1, 2);
```

## 条件分岐 CASE
```mysql {hl_lines=["2-5"],linenostart=1}
SELECT
  CASE
    WHEN active_flag = true THEN 'ON'
    WHEN active_flag = false THEN 'OFF'
    ELSE '' END
FROM
  sample_table;
```
---
```mysql {hl_lines=["5-12"],linenostart=1}
SELECT
    id,
    name,
    score,
    CASE
        WHEN score >= 80 THEN 'Excellent'
        WHEN 80 > score AND score >= 70 THEN 'Good'
        WHEN 70 > score AND score >= 60 THEN 'Average'
        WHEN 60 > score AND score >= 50 THEN 'Below Average'
        WHEN 50 > score THEN 'Poor'
        ELSE ''
    END AS 'evaluation'
FROM
    scores;
```

## 便利SQL

### SELECT句の値を変数にセット
```mysql {hl_lines=["4-6"],linenostart=1}
SELECT
    100,
    'sample'
INTO
    @HOGE,
    @FOO;

SELECT @HOGE, @FOO;
+-------+--------+
| @HOGE | @FOO   |
+-------+--------+
|   100 | sample |
+-------+--------+
```

### SELECT したデータを INSERT
```mysql
INSERT INTO
    sample_table_1 (sample_column_1, sample_column_2, sample_column_3)
SELECT
    sample_column_1,
    sample_column_2,
    sample_column_3
FROM
    sample_table_2;
```
一部の値を固定値で登録（以下の例では sample_column_3 に 固定値 'hoge' を登録
```mysql {hl_lines=[6],linenostart=1}
INSERT INTO
    sample_table_1 (sample_column_1, sample_column_2, sample_column_3)
SELECT
    sample_column_1,
    sample_column_2,
    'hoge' AS sample_column_3
FROM
    sample_table_2;
```
### 特定のカラムの値が重複しているレコードを抽出 HAVING/GROUP BY
```mysql {hl_lines=["6-9"],linenostart=1}
SELECT
    sample_column,
    COUNT(*) AS count
FROM
    sample_table
GROUP BY
    sample_column
HAVING
    count > 1
ORDER BY
    sample_column;
```

## 置換

### 文字列を置換して更新

```mysql {hl_lines=[4],linenostart=1}
UPDATE
    sample_table
SET
    sample_column = REPLACE(sample_column, 'Search String', 'Replacement String')
WHERE
    sample_column LIKE '%Search String%';
```
## 改行コード

### Carriage Return (CR)
```mysql
\r
```
```mysql
CHAR(13)
```
### Line Feed (LF)
```mysql
\n
```
```mysql
CHAR(10)
```

### 改行コードを半角スペースに置換

```mysql {hl_lines=[2],linenostart=1}
SELECT
    REPLACE(REPLACE(REPLACE(sample_column, '\r\n', ' '), '\r', ' '), '\n', ' ')
FROM
    sample_table;
```

```mysql {hl_lines=[2],linenostart=1}
SELECT
    REPLACE(REPLACE(REPLACE(sample_column, CHAR(13) + CHAR(10), ' '), CHAR(13), ' '), CHAR(10), ' ')
FROM
    sample_table;
```


```mysql {hl_lines=[4],linenostart=1}
UPDATE
    sample_table
SET
    sample_column = REPLACE(REPLACE(REPLACE(sample_column, '\r\n', ' '), '\r', ' '), '\n',' ');
```

```mysql {hl_lines=[4],linenostart=1}
UPDATE
    sample_table
SET
    REPLACE(REPLACE(REPLACE(sample_column, CHAR(13) + CHAR(10), ' '), CHAR(13), ' '), CHAR(10), ' ');
```

## VIEW

### VIEWの一括DROP文

```mysql
SELECT
    CONCAT('DROP VIEW ', table_name, ';') AS view_drop_sql
FROM
    information_schema.tables
WHERE
    table_schema NOT IN (
        'mysql',
        'perfomance_schema',
        'information_schema'
    )
    AND table_type = 'VIEW'
ORDER BY
    table_schema,
    table_name;
```

### VIEWの一覧を作成するCREATE文の一覧

```mysql
SELECT
    CONCAT(TABLE_SCHEMA, '.', TABLE_NAME) AS target_view,
    CONCAT(
        'CREATE ALGORITHM=UNDEFINED DEFINER=`',
        DEFINER,
        '` SQL SECURITY ',
        SECURITY_TYPE,
        ' VIEW `',
        TABLE_NAME,
        '` AS ',
        VIEW_DEFINITION,
        ';'
    ) AS create_view
FROM
    information_schema.views;
```

## その他

### SQLの実行計画
```mysql
EXPLAIN SELECT * FROM sample_table WHERE sample_flag = 1;
```

### データベースへのコネクション数
{{<hint info>}}
threads へのアクセスには相互排他ロックは必要なく、サーバーパフォーマンスへの影響は最小です。INFORMATION_SCHEMA.PROCESSLIST と SHOW PROCESSLIST では相互排他ロックが必要になるため、パフォーマンスの低下につながります。
https://dev.mysql.com/doc/refman/5.6/ja/performance-schema-threads-table.html
{{< /hint >}}
```mysql
SELECT
    processlist_host,
    COUNT(*) AS connection_count
FROM
    performance_schema.threads
WHERE
    TYPE = 'FOREGROUND'
GROUP BY
    processlist_host
ORDER BY
    processlist_host;
```

### テーブルのロック状態
ストレージエンジンがInnoDBの場合
```mysql
SELECT * FROM information_schema.innodb_locks;
```

### トランザクション

```mysql
BEGIN;

UPDATE sample_table SET email = 'sample1@email.com' WHERE id = 1;
UPDATE sample_table SET email = 'sample1@email.com' WHERE id = 2;

-- 失敗した場合は ROLLBACK;

COMMIT;
```
or
```mysql
START TRANSACTION;

UPDATE sample_table SET email = 'sample1@email.com' WHERE id = 1;
UPDATE sample_table SET email = 'sample1@email.com' WHERE id = 2;

-- 失敗した場合は ROLLBACK;

COMMIT;
```


## AUTO_INCREMENT（自動採番）

### AUTO_INCREMENT（自動採番）の値を確認
```mysql
SELECT
    AUTO_INCREMENT
FROM
    INFORMATION_SCHEMA.TABLES
WHERE
    TABLE_SCHEMA = 'sample_database'
    AND TABLE_NAME = 'sample_table';
```
---
```mysql
SHOW TABLE STATUS WHERE name = 'sample_table';
```
---
```mysql
SHOW TABLE STATUS LIKE 'sample_table_prefix%';
```
### AUTO_INCREMENT（自動採番）の値を変更
```mysql
ALTER TABLE sample_table AUTO_INCREMENT = 1000;
```



## 設定
### トランザクションのタイムアウト
```mysql
SHOW GLOBAL VARIABLES LIKE '%lock_wait_time_out'
+----------------------+--------+
| Variable_name        | Value  |
+----------------------+--------+
| lock_wait_timeout_ms | 300000 |
+----------------------+--------+
1 row in set (0.01 sec)
```

## 暗号化

[MySQL Reference Manual - Encryption and Compression Functions](https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html)
```mysql
SET block_encryption_mode = 'aes-256-cbc';
SET @key_str = SHA2('My secret passphrase',512);
SET @init_vector = RANDOM_BYTES(16);
SET @crypt_str = AES_ENCRYPT('text',@key_str,@init_vector);
SELECT AES_DECRYPT(@crypt_str,@key_str,@init_vector);
+-----------------------------------------------+
| AES_DECRYPT(@crypt_str,@key_str,@init_vector) |
+-----------------------------------------------+
| text                                          |
+-----------------------------------------------+
```

# References
* https://qiita.com/devopsCoordinator/items/9b70e506150888e190be
* https://qiita.com/CyberMergina/items/f889519e6be19c46f5f4
* https://qiita.com/Tocyuki/items/cb613c51a0e9c6e4f23c
* https://qiita.com/marnie_ms4/items/576055abc355184c51a1
* https://qiita.com/fururun02/items/148a1eee68a1fb978f06
* https://qiita.com/dodonki1223/items/776a3520e45626773c60
