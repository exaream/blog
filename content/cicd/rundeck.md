---
date: 2022-01-13
lastmod: ["lastmod"]
title: Rundeck
linkTitle: Rundeck
description: Rundeck
tags: ["Rundeck", "Job Scheduler"]
draft: true
---

# Rundeck

### 概要

- ジョブ管理ソフトウェア。ジョブの作成・実行・管理・スケジューリング等が可能。
- 開発元：PagerDuty, Inc.

### ライセンス

- オープンソース版 (無償) Rundeck Community ※ [Apache Software License 2.0](https://www.apache.org/licenses/LICENSE-2.0)
- エンタープライズ版 (有償) Rundeck Enterprise

### 特徴

- Web画面でのジョブ・スケジュールの設定や実行結果の確認。
- ジョブの複数サーバでの実行 (SSHを使用)。
- ジョブの依存関係の管理。
- ジョブの重複実行の抑制。
- ジョブの手動実行。
- さまざまな種類の通知に対応。

### 機能比較

- エンタープライズ版 固有の機能 ※ [Rundeck vs Commercial Feature Comparison](https://www.rundeck.com/rundeck-vs-commercial)
  - ユーザー管理(ACLを含む)。
  - ジョブのフローの視覚化。
  - シングル・サインオン。

## ローカル環境の構築


## 使用方法

### プロジェクトの作成

1. 下記URLにアクセス。  
  [http://127.0.0.1:4440/user/login](http://127.0.0.1:4440/user/login)
1. ユーザー名とパスワードを入力しログイン。  
   Username: admin  
   Password: admin
1. ホーム画面の `新しいプロジェクト+` ボタン押下。
1. `Project Name` 欄に任意のプロジェクト名を入力。  
   Project Name: FooProject
1. `作成`ボタン押下。
1. 下記メッセージが表示されればOK。  
```text
Project FooProject was created. Node Sources can be added to define project nodes
```

### SSH認証の設定

1. 画面左下の `PROJECT SETTINGS` > `KEY STORAGE`。
1. `Add or Upload a Key` ボタン押下。
1. 以下の内容を設定。
|項目名|設定値|
|---|---|
|Key Type|Private Key|
|ファイルを選択|ファイルを指定し `ファイルをアップロード`|
|Name|任意|
1. `Save` ボタン押下。

### ノードの作成

1. 画面左下の `PROJECT SETTINGS` > `EDIT NODES`。
1. `Add a new Node Source` ボタン押下。
1. `File` を選択。
1. `Sources` タブ内で以下を設定。
|項目名|設定値|
|---|---|
|Format|`resourcexml`|
|File Path|`/home/rundeck/sample.xml`|
||`Generate`を選択|
||`Include Servder Node`を選択|
||`Require File Exists`を選択|
||`Writeable`を選択|


1. `Save` ボタン押下。
1. `Save` ボタン押下。(ノード編集画面)
1. `Edit` タブを選択し、`Modify` ボタンを押下。
1. 下記内容を入力。


```text
├── .ssh
│   ├── .gitignore
│   └── config
├── context
│   ├── .gitignore
│   ├── private_key.yml
│   └── rundeck-team-incoming-webhook-plugin-0.6.jar
├── .env
├── .gitignore
└── docker-compose.yml
```

.ssh/.gitignore
```text
*
!.gitignore
!config
```
.ssh/config
```text
Host github.com
   HostName github.com
   identityFile ~/.ssh/id_ed25519
	User git
	IdentitiesOnly yes
	Port 22
	TCPKeepAlive yes
```
context/.gitignore
```text
id_ed25519*
```
context/private_key.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <node name="node_private_key"
        description="sample node"
        tags=""
        hostname="foo_batch:22"
        username="rundeck"
        ssh-authentication="privateKey"
        ssh-key-passphrase-storage-path="keys/project/FooProject/id_ed25519"
        />
</project>
```

.env
```text
# rundeck
JVM_MAX_RAM_PERCENTAGE=75
RUNDECK_DATABASE_DRIVER=org.mariadb.jdbc.Driver
RUNDECK_DATABASE_USERNAME=rundeck
RUNDECK_DATABASE_PASSWORD=rundeck
RUNDECK_DATABASE_URL=jdbc:mysql://rundeck_mysql/rundeck?autoReconnect=true&useSSL=false
RUNDECK_GRAILS_URL=http://localhost:4440
RUNDECK_MAIL_SMTP_HOST=mailcatcher
RUNDECK_MAIL_SMTP_PORT=1025
RUNDECK_MAIL_SMTP_USERNAME=null
RUNDECK_MAIL_SMTP_PASSWORD=null
RUNDECK_MAIL_FROM=mailcatcher@example.com
TZ=Asia/Tokyo

# rundeck_mysql
MYSQL_ROOT_PASSWORD=環境変数を設定
MYSQL_DATABASE=環境変数を設定
MYSQL_USER=環境変数を設定
MYSQL_PASSWORD=環境変数を設定
```

.gitignore
```text
/dbdata
```

docker-compose.yaml
```yaml
version: '3'

services:

  rundeck:
    container_name: rundeck
    image: rundeck/rundeck:4.0.0
    environment:
      - JVM_MAX_RAM_PERCENTAGE
      - RUNDECK_DATABASE_DRIVER
      - RUNDECK_DATABASE_USERNAME
      - RUNDECK_DATABASE_PASSWORD
      - RUNDECK_DATABASE_URL
      - RUNDECK_GRAILS_URL
      - RUNDECK_MAIL_SMTP_HOST
      - RUNDECK_MAIL_SMTP_PORT
      - RUNDECK_MAIL_SMTP_USERNAME
      - RUNDECK_MAIL_SMTP_PASSWORD
      - RUNDECK_MAIL_FROM
      - TZ
    volumes:
      - ${RUNDECK_LICENSE_FILE:-/dev/null}:/home/rundeck/etc/rundeckpro-license.key
      - ./context/id_ed25519:/home/rundeck/.ssh/id_ed25519
      - ./context/id_ed25519.pub:/home/rundeck/.ssh/id_ed25519.pub
      - ./context/private_key.xml:/home/rundeck/home/rundeck/private_key.xml
      - ./context/rundeck-team-incoming-webhook-plugin-0.6.jar:/home/rundeck/libext/rundeck-team-incoming-webhook-plugin-0.6.jar
    depends_on:
      - rundeck_mysql
    ports:
      - 4440:4440
    networks:
      - container-link

  rundeck_mysql:
    container_name: rundeck-mysql
    image: mysql:5.7
    expose:
      - 3306
    environment:
      - MYSQL_ROOT_PASSWORD
      - MYSQL_DATABASE
      - MYSQL_USER
      - MYSQL_PASSWORD
    volumes:
      - ./dbdata:/var/lib/mysql
    networks:
      - container-link

networks:
  # Prevent generating "default" network automatically.
  default:
    external: true
    name: bridge
  # Network segment of inter-container communication.
  container-link:
    name: sample_network
```


Memo
```shell
docker exec -it rundeck sh
sudo apt-get update # TODO Move to Dockerfile
sudo apt-get install ssh-copy-id # TODO Move to Dockerfile
# Register the public key
ssh-copy-id rundeck@[Target Container Name] # TODO Move to Dockerfile
```
```shell
ssh rundeck@Target Container Name]
```

Access http://localhost:4440/user/login