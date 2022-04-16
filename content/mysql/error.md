---
date: 2020-01-01
lastmod: ["lastmod"]
title: MySQL Error
LinkTitle: Error
description: MySQL Error
#tags: ["MySQL", "Error"]
draft: true
---

# MySQL Error

## ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction

```mysql
SHOW GLOBAL VARIABLES LIKE 'innodb_lock_wait_timeout';

SHOW PROCESSLIST;

SHOW INNODB STATUS;

KILL [transaction's id];
```


* MySQLのセッションがトランザクションを使用している時に、ロックがかかった状態のまま、他のセッションがそのテーブルに対して更新しようとして、timeoutが発生。
* インデックス、ユニークキー制約のないカラムに対して、トランザクションを貼ったまま放置すると、行ロックではなくてテーブル・ロックがかかってしまう。
* トランザクションを貼った状態で INSERT や UPDATE し COMMIT 前にセッションが切れた場合、SELECTしても何もないため、その後 INSERT や UPDATE を使用とすると発生。

* https://nagaikiyo.hatenablog.com/entry/2013/03/11/205536
* https://libitte.hatenablog.jp/entry/20141129/1417254010