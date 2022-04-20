---
date: 2020-01-01
lastmod: ["lastmod"]
title: PostgreSQL
LinkTitle: PostgreSQL
description: PostgreSQL
tags: ["PostgreSQL", "SQL"]
---

# PostgreSQL

## How to change a port number of PostgreSQL on macOS
Change the port number of PostgreSQL.  
(Change the version number 13 to your PostgreSQL version in the following command.)
```shell
sudo vi Library/PostgreSQL/13/data/postgresql.conf
```

```shell
port = 5432                             # (change requires restart)
↓
port = 5433                             # (change requires restart)
```

Restart PostgreSQL.  
(Change the version number 13 to your PostgreSQL version in the following command.)
```shell
$ sudo launchctl stop com.edb.launchd.postgresql-13
$ sudo launchctl start com.edb.launchd.postgresql-13
```

## PostgreSQL's Information

### Table Schema

```postgresql
SELECT
    col.table_name,
    col.ordinal_position, -- No.
    col.column_name,
    col.data_type,
    col.character_maximum_length, -- 文字数(文字列型)
    col.character_octet_length, -- バイト数(文字列型)
    col.numeric_precision, -- 桁数(数値型)
    col.numeric_scale, -- 小数部(数値型)
    col.datetime_precision, -- ミリ秒以下(日付型)
    col.interval_type, -- インターバルの単位(日付型)
    col.is_nullable, -- NULLを許可するか否か
    col.column_default -- 初期値
FROM
    information_schema.columns col
WHERE
    col.table_name = 'sample_table' -- 対象テーブル名を指定
ORDER BY
    col.table_name,
    col.ordinal_position
```