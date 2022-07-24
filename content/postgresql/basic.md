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
$ psql -h sample_hostname -p sample_portnumber -U sampleuser sample_db
```
Run SQL
```shell
$ psql --file=/path/to/sample.sql
```

### Options


|option|short|description|
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

## PostgreSQL's Command

|Description|Command|
|---|---|
|Help|`\?`|
|Show DBs|`\l`|
|Switch DB|`\c sample_db`|
|Show tables|`\d`, `\dt`, `\d+`, `\dt+`|
|Describe table definition|`\d sample_tbl`|
|Show `CREATE TABLE`|`pg_dump sample_db -U sampleuser -s -t sample_tbl`|
|Show indexs|`\d sample_tbl`|
|Swich display mode|<span class="code">\x<br>SELECT * FROM sample_tbl;</span>|
|Run SQL file|`\i /path/to/sample.sql`|
|Inport TSVfrom |`COPY sample_tbl '/absolute/path/to/sample.tsv' (delimiter '', format csv, header true);`|
|Measure SQL time|`\time on`|
|Begin log output|`\o sample.log`|
|End log output|`\o`|
|Copy definitions & Create table|`CREATE TABLE copy_tbl (LIKE org_tbl)`|
|Show execution plan|Add `EXPLAIN ` before SQL|
|Check server info|`\conninfo`<br>You are connected to database "sample_db" as user "sampleuser" on host "sample_host" (address "XXX.XXX.XXX.XXX") at port "XXXX".|
|Check process|`SELECT * FROM pg_stat_activity;`|

## PostgreSQL's Information

### Default Connection Information
- Set values of connection information as an environment variable.
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

## Backup & Restore

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

### Backup all DBs and related info
- Can NOT specify the format when using `pg_dumpall`.
- All dumps are in plain text format.
```shell
$ sudo -i -u sampleuser

$ pg_dumpall --file all.sql
or
$ pg_dumpall > all.sql
```

### Restore
- Can restore with `psql` command if the dump file is in plain text format.
```shell
$ psql --file=/path/to/sample.sql
```


## CREATE TABLE

### id, created_at, updated_at
- Use UUID as the primary key.
#### Simple Version

```postgres
CREATE TABLE sample_tbl (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP -- Update by program
);
```

#### More Accurate Version

```postgresql
CREATE TABLE sample_tbl (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

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

## Timezone

### Check current timezone

```shell
$ SHOW TIMEZONE;
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