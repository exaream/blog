---
date: 2020-01-01
lastmod: ["lastmod"]
title: PostgreSQL Basic
LinkTitle: Basic
description: PostgreSQL Basic
tags: ["PostgreSQL", "SQL"]
---

# PostgreSQL Basic

## `psql` command

### Usage
```shell
psql [OPTION]... [DBNAME [USERNAME]]
```

Access DB
```shell
$ psql --host=sample_hostname --port=sample_portnumber --username=sampleuser sample_db
or
$ psql -h sample_hostname -p sample_portnumber -U sampleuser sample_db
```
Run SQL file
```shell
$ psql --file=/path/to/sample.sql
or
$ psql -f /path/to/sample.sql
```

Check Version
```shell
$ psql --version
or
$ psql -V
```

### Options


|option|option|description|
|---|---|---|
|`-h`|`--host=HOSTNAME`|database server host or socket directory (default: "local socket")|
|`-p`|`--port=PORT`|database server port (default: "5432")|
|`-U`|`--username=USERNAME`|database user name (default: "root")|
|`-w`|`--no-password`|never prompt for password|
|`-W`|`--password`|force password prompt (should happen automatically)|
|`-c`|`--command=COMMAND`|run only single command (SQL or internal) and exit|
|`-d`|`--dbname=DBNAME`|database name to connect to (default: "root")|
|`-f`|`--file=FILENAME`|execute commands from file, then exit|
|`-l`|`--list`|list available databases, then exit|
|`-v`|`--set=`, `--variable=NAME=VALUE`|set psql variable NAME to VALUE<br>(e.g., -v ON_ERROR_STOP=1)<br>|
|`-V`|`--version`|output version information, then exit|
|`-X`|`--no-psqlrc`|do not read startup file (~/.psqlrc)|
|`-1` ("one")|`--single-transaction`|execute as a single transaction (if non-interactive)|
|`-?`|`--help[=options]`|show this help, then exit|
|-|`--help=commands`|list backslash commands, then exit|
|-|`--help=variables`|list special variables, then exit|

## Commands using `\(backslash)`

|Command|Description|Remarks|
|---|---|---|
|`\?`|Help|-|
|`\l`|Show DBs|-|
|`\c sample_db`|Switch DB|-|
|`\d`, `\dt`, `\d+`, `\dt+`|Show tables|-|
|`\d sample_tbl`|Describe table definition|-|
|`\d sample_tbl`|Show indexs|-|
|`\x`|Swich display mode|<span class="code">\x<br>SELECT * FROM sample_tbl;</span>|
|`\i sample.sql`|Run SQL file|-|
|`\time on`|Measure SQL time|-|
|`\o sample.log`|Begin log output|-|
|`\o`|End log output|-|
|`\conninfo`|Check server info|-|
|`\q`|Quit||

## PostgreSQL's Info

### Default Connection Info
- Set values of connection info as an environment variable.
- Can also set the password on `~/.pgpass`
```shell
export PGDATABASE=sample_db
export PGHOST=sample_host
export PGPORT=XXXX
export PGUSER=sampleuser
export PGPASSWORD=samplepassword
```

### Table List
```postgresql
SELECT
  table_catalog,
  table_schema,
  table_name,
  table_type
FROM
  information_schema.tables;
```

### Column List

```postgresql
SELECT
    table_name,
    ordinal_position, -- No.
    column_name,
    data_type,
    character_maximum_length, -- 文字数(文字列型)
    character_octet_length, -- バイト数(文字列型)
    numeric_precision, -- 桁数(数値型)
    numeric_scale, -- 小数部(数値型)
    datetime_precision, -- ミリ秒以下(日付型)
    interval_type, -- インターバルの単位(日付型)
    is_nullable, -- NULLを許可するか否か
    column_default -- 初期値
FROM
    information_schema.columns
WHERE
    table_name = 'sample_tbl' -- 対象テーブル名を指定
ORDER BY
    table_name,
    ordinal_position
```

### Index List

```postgresql
SELECT
  schemaname AS schema_name,
  tablename AS table_name,
  indexname AS index_name,
  indexdef AS index_definition
FROM
  pg_indexes
ORDER BY
  schemaname,
  tablename,
  indexname;
```

### Number of records in each table

```postgresql
SELECT
  pg_class.relname,
  (pg_class.reltuples / 1) AS count
FROM
  pg_stat_user_tables
  INNER JOIN pg_class ON pg_stat_user_tables.relname = pg_class.relname
ORDER BY
  CAST(pg_class.reltuples AS numeric) DESC;
```

### Process List
```postgresql
SELECT * FROM pg_stat_activity;
```

## `pg_dump` command

### Show info related `CREATE TABLE`, `CREATE TRIGGER`

```shell
$ pg_dump sample_db -U sampleuser -s -t sample_tbl
```

### Backup one DB

```shell
$ pg_dump -U sampleuser --format=plain --file=/path/to/sample.sql sample_db
```

```shell
$ pg_dump -U sampleuser --format=plain sample_db > /path/to/sample.sql
```

```shell
$ pg_dump -U sampleuser --format=tar --file=/path/to/sample.sql sample_db
```

### Restore
- Can restore with `psql` command if the dump file is in plain text format.
```shell
$ psql --file=/path/to/sample.sql
```

## `pg_dumpall` command

### Backup all DBs
- All dumps are in plain text format.
- Can NOT specify the format when using `pg_dumpall`.
```shell
$ sudo -i -u sampleuser

$ pg_dumpall --file all.sql
or
$ pg_dumpall > all.sql
```

## `EXPLAIN` (Execution Plan)
- Add `EXPLAIN` before SQL
```postgresql
EXPLAIN SELECT * FROM sample_tbl WHERE id = XXXXX;
```

## `CREATE TABLE`


### Copy definitions & Create table

```postgresql
CREATE TABLE copy_tbl (LIKE org_tbl);
```

### `WITHOUT OIDS`
- オブジェクト識別子(OID)は PostgreSQLの内部で様々なシステムテーブルのプライマリキーとして使用される。
- `default_with_oids` 設定が `false` の場合、または、テーブル作成時に `WITHOUT OIDS` を指定しない場合は、自動でテーブルに追加される。
- 符号なし4バイト整数。大規模なデータに対しては十分な大きさではないため要注意。

### Define id and timestamp
- Use UUID as the primary key.
#### Simple Version

```postgres
CREATE TABLE sample_tbl (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP -- Update by program
) WITHOUT OIDS;
```

#### More Accurate Version

```postgresql
CREATE TABLE sample_tbl (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
) WITHOUT OIDS;

CREATE TRIGGER refresh_users_updated_at_step1
    BEFORE UPDATE ON users FOR EACH ROW
    EXECUTE PROCEDURE refresh_updated_at_step1();
CREATE TRIGGER refresh_users_updated_at_step2
    BEFORE UPDATE OF updated_at ON users FOR EACH ROW
    EXECUTE PROCEDURE refresh_updated_at_step2();
CREATE TRIGGER refresh_users_updated_at_step3
    BEFORE UPDATE ON users FOR EACH ROW
    EXECUTE PROCEDURE refresh_updated_at_step3();

CREATE FUNCTION refresh_updated_at_step1() RETURNS trigger AS
$$
BEGIN
  IF NEW.updated_at = OLD.updated_at THEN
    NEW.updated_at := NULL;
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
    
CREATE FUNCTION refresh_updated_at_step2() RETURNS trigger AS
$$
BEGIN
  IF NEW.updated_at IS NULL THEN
    NEW.updated_at := OLD.updated_at;
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE FUNCTION refresh_updated_at_step3() RETURNS trigger AS
$$
BEGIN
  IF NEW.updated_at IS NULL THEN
    NEW.updated_at := CURRENT_TIMESTAMP;
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;
```

### Partition

```postgresql
CREATE TABLE sample_tbl (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP -- Update by program
) PARTITION BY RANGE (created_at) WITHOUT OIDS;

SELECT create_parent(
    p_parent_table => concat(current_schema(), '.sample_tbl'),
    p_control => 'created_at',
    p_type => 'native',
    p_interval=> 'monthly'
);
```

## CREATE INDEX

```postgresql
-- CREATE INDEX index_name ON table_name(column_name, ...)
CREATE INDEX sample_tbl_idx_01 ON sample_tbl(sample_column_1, sample_column_2);
```

## Timezone

### Check current timezone

```shell
SHOW TIMEZONE;
```

### Check timezones that can be set
```shell
SELECT * FROM pg_timezone_names;
```
### Set new timezone
```shell
ALTER DATABASE exsampledb SET timezone TO 'Asia/Tokyo';
```

### Apply the change
```shell
SELECT pg_reload_conf();
```

## Export

### Export to CSV file
- Use tab as a delimiter. `DELIMITER ','`
- Enclose all fields in quote. `FORCE QUOTE *`
- Output field names as header. `HEADER`
- Change NULL to '' (empty string). `NULL AS ''`
```postgresql
COPY sample_tbl FROM '/absolute/path/to/sample.tsv' WITH CSV DELIMITER ',' FORCE QUOTE * NULL AS '' HEADER;
```
or
```shell
$ psql sample_db -c "SELECT * FROM sample_tbl" -A -F, > sample.csv
```


### Export to TSV file
- Use tab as a delimiter. `DELIMITER E'\t'`
- Enclose all fields in quote. `FORCE QUOTE *`
- Output field names as header. `HEADER`
- Change NULL to '' (empty string). `NULL AS ''`
```postgresql
COPY sample_tbl FROM '/absolute/path/to/sample.tsv' WITH CSV DELIMITER E'\t' FORCE QUOTE * NULL AS '' HEADER;
```
or
```shell
$ psql sample_db -c "SELECT * FROM sample_tbl" -A -F $'\t' > sample.tsv
```
## Import

### Import from CSV file

```postgresql
COPY sample_tbl FROM '/absolute/path/to/sample.tsv' WITH CSV DELIMITER ',';
```

### Import from TSV file

```postgresql
COPY sample_tbl FROM '/absolute/path/to/sample.tsv' WITHC CSV DELIMITER E'\t';
```

## Trouble Shooting
### How to change a port number of PostgreSQL on macOS
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


## References
- [PostgreSQL Documentation](https://www.postgresql.org/docs/current)
- [PostgreSQL Documentation(Japanese)](https://www.postgresql.jp/document/current/html)
- [PostgreSQL データ型](https://www.postgresql.jp/document/current/html/datatype.html)
- [PostgreSQL: Documentation: 14: 28.2. The Statistics Collector](https://www.postgresql.org/docs/14/monitoring-stats.html)
- [Postgres と MySQL における id, created_at, updated_at に関するベストプラクティス](https://zenn.dev/mpyw/articles/rdb-ids-and-timestamps-best-practices)
- [MySQLとPostgreSQLコマンド比較表](https://qiita.com/aosho235/items/c657e2fcd15fa0647471)
- [PostgreSQL - タイムゾーンをDBに設定する](https://qiita.com/zkangaroo/items/93be2d4504c3d1d5f185)

### TODO
- [PostgreSQL Isolation について](https://www.kimullaa.com/entry/2020/03/14/134232)
- [【PostgreSQL】 jsonb型カラムをSQLで検索・集計したい](https://blog.serverworks.co.jp/postgres_jsonb_search)