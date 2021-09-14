---
date: 2020-01-01
lastmod: ["lastmod"]
title: Ubuntu
tags: ["Linux","Ubuntu","apt-get"]
---

## ```apt-get```

|command|description|
|---|---|
|```apt-get install <package>```|指定パッケージのインストールと更新|
|```apt-get update```|パッケージ・リストを更新|
|```apt-get upgrade```|インストール済パッケージを更新|
|```apt-get dist-upgrade```|追加で必要なパッケージをインストール、不要パッケージを削除、ディストリビューションのバージョンを更新(セキュリティパッチをあてる場合などにも使用)|
|```apt-get remove <package>```|指定パッケージを削除|
|```apt-get autoremove```|未使用パッケージを削除 (設定ファイルは削除しない)|
|```apt-get purge <package>```|未使用パッケージを削除 (設定ファイルも削除する)|
|```apt-get clean```|パッケージをインストールした際のアーカイブ・ファイル(*.tar.gzなど)を削除|
|```apt-get autoclean```|未使用パッケージのアーカイブ・ファイルを削除|

## ```apt-cache```
|command|description|
|---|---|
|```apt-cache search <query>```|パッケージを検索 (パッケージ名と説明が ```<query>``` の対象)|
|```apt-cache policy <query>```|パッケージを検索 (インストール可能なバージョンの表示)|
|```apt-cache madison <query>```|パッケージを検索 (インストール可能なバージョンの一覧)|

## ```dpkg```
|command|description|
|---|---|
|```dpkg -l <package>```|インストール済パッケージの一覧を表示、```<package>``` を省略した場合 全パッケージを表示|
|```dpkg -L <package>```|インストール時のファイルの一覧を表示|

## References
* [Ubuntu apt-get まとめ](https://qiita.com/white_aspara25/items/723ae4ebf0bfefe2115c)