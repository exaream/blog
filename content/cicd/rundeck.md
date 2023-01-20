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
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <node name="node_private_key"
        description="sample node"
        tags=""
        hostname="foo_batch:22"
        username="rundeck"
        ssh-authentication="privateKey"
        ssh-key-passphrase-storage-path="keys/project/FooProject/id_rsa"
        />
</project>
```
1. `保存` ボタン押下。

`ssh-key-passphrase-storage-path` の確認
1. 画面左下の `PROJECT SETTINGS` > `KEY STORAGE`。
1. `Storage path` 欄に以下があることを確認。  
   `keys/project/FooProject/id_rsa`

### その他
- ログのマスキング：ログ・フィルターを使用した場合、Rundeck 側に保存されたログ・ファイル自体がマスキングされるため注意。
- チャット通知用のプラグイン
  - Microsoft Teams notification Plugin (ジョブ名が日本語の場合も文字化けなし)

