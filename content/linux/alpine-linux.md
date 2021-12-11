---
date: 2020-01-01
lastmod: ["lastmod"]
title: Alpine Linux
tags: ["Linux","Alpine Linux"]
---

# Alpine Linux

## Improvement of `date` command

### Before
```shell
$ LAST_MONTH=`date -d "1970.01.01-00:00:$(( $( date +\%s ) - $(( 24 * 60 * 60 * 30 )) ))" +%Y%m`
$ echo $LAST_MONTH
```

### Add `coreutils` package
```shell
apk add --update coreutils && rm -rf /var/cache/apk/*
```

### After
```shell
$ LAST_MONTH=`date +\%Y\%m -d 'last month'`
$ echo $LAST_MONTH
```
