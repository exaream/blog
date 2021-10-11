---
date: 2020-01-01
lastmod: ["lastmod"]
title: docker-compose
tags: ["Docker", "docker-compose"]
---

### Run containers
* カレントディレクトリの `docker-compose.yml` に記載したコンテナ群を起動
* 初回起動時にコンテナ群をつなぐユーザ定義・ブリッジ・ネットワークも自動生成

* フォアグラウンドモードで起動する場合(ターミナルにコンテナログを出力)
```shell
$ docker-compose up
```
* バックグラウンドモードで起動する場合
```shell
$ docker-compose up -d
```

### Stop containers
* フォアグラウンドモードで起動した場合
  * ターミナルで Ctrl+C 押下
* バックグラウンドモードで起動した場合
```shell
$ docker-compose stop
```

### Rebuild an image
```shell
$ docker-compose build
```

### Remove containers and networks
```shell
$ docker-compose down
```

### Stop and remove all (containers, images, volumes, networks) 滅びの呪文

```shell
docker-compose down --rmi all --volumes --remove-orphans
```