---
date: 2020-01-01
lastmod: ["lastmod"]
title: docker-compose
tags: ["Docker", "docker-compose"]
---

### コンテナ群の起動
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

### コンテナ群の停止
* フォアグラウンドモードで起動した場合
  * ターミナルで Ctrl+C 押下
* バックグラウンドモードで起動した場合
```shell
$ docker-compose stop
```

### イメージの再ビルド
```shell
$ docker-compose build
```

### コンテナ群とネットワークの削除
```shell
$ docker-compose down
```
