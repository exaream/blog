---
date: 2023-01-19
lastmod: ["lastmod"]
title: Multiple GitHub Account on 1 PC
linkTitle: Multiple GitHub Account on 1 PC
description: Multiple GitHub Account on 1 PC
tags: ["GitHub","Multiple Account","Configuration"]
---

# 複数のGitHubアカウントを1つのPCで使用

## 概要
- 複数のGitHubアカウントを1つのPC内で自動的に切り分ける方法を説明
- 以下のディレクトリ `account1` と　`account2` に異なるGitHubアカウントを設定

ディレクトリ構成
```text
~/source
└── github
    ├── account1
	│   ├── repository1
	│   └── repository2
    └── account2
	    ├── repository3
	    └── repository4
```

## 1. 公開鍵と秘密鍵を作成

- まず `account1` 用の公開鍵と秘密鍵を作成

### `ssh-keygen` のオプション
|option|説明|備考|
|---|---|---|
|`-t`|鍵の暗号化方式|今回は強度の高い `ed25519` を選択|
|`-P`|パスワード|今回は空を指定|
|`-C`|任意のコメント|今回はGitHubのメールアドレスを指定|
|`-f`|出力するファイル名|今回は `id_暗号化方式_任意の識別子` を指定|

### `ssh-keygen` で作成
内容
```shell
$ cd ~/.ssh
$ ssh-keygen -t [暗号化方式] -P '' -C [追加するアカウントのメール] -f [秘密鍵のファイル名]
```
実例
```shell
$ cd ~/.ssh
$ ssh-keygen -t ed25519 -P '' -C sample@gmail.com -f id_ed25519_github_account1
$ ls -l
config
id_ed25519_github_account1     # account1 用の秘密鍵
id_ed25519_github_account1.pub # account1 用の公開鍵
known_hosts
```

### 公開鍵の内容をコピー
**Windows**  
内容
```shell
$ clip < ~/.ssh/[公開鍵のファイル名]
```
実例
```shell
$ clip < ~/.ssh/id_ed25519_github_account1.pub
```
**Mac**  
内容
```shell
$ pbcopy < ~/.ssh/[公開鍵のファイル名]
```
実例
```shell
$ pbcopy < ~/.ssh/id_ed25519_github_account1.pub
```

## 2.GitHubアカウントに公開鍵を設定

1. `account1` 用のGitHubアカウントでログイン https://github.com/login
1. SSHキーの設定画面にアクセス https://github.com/settings/ssh
1. `NewSSHkey` 押下
|項目|内容|
|---|---|
|Title|任意の値を入力(識別できればOK)|
|Key Type|Authentication Key|
|Key|コピーした公開鍵の内容を貼り付け|
1. `AddSSHKey` 押下

## 3. SSH設定ファイルにGitHubアカウント を追加

```shell
$ vi ~/.ssh/config
```

内容
```shell
Host [PC内で切り分けるためのホスト名]
	HostName github.com
	IdentityFile ~/.ssh/[追加するアカウント用の秘密鍵のファイル名]
	User git
	IdentitiesOnly yes
	Port 22
	TCPKeepAlive yes
```
実例
```shell
Host github.account1
	HostName github.com
	IdentityFile ~/.ssh/id_ed25519_github_account1
	User git
	IdentitiesOnly yes
	Port 22
	TCPKeepAlive yes
```

## 4.GitHubアカウントとSSH接続ができているか確認

- 以下のように表示されていればOK
内容
```shell
$ ssh -T [PC内で切り分けるためのホスト名]
Hi [追加するアカウントのユーザー名]! You've successfully authenticated, but GitHub does not provide shell access.
```

```shell
$ ssh -T github.account1
Hi account1! You've successfully authenticated, but GitHub does not provide shell access.
```

## 5. ディレクトリごとにGitHubアカウントが切り替わるよう設定

### 追加するGitHubアカウント用の `.gitconfig` ファイルを作成

- ホームディレクトリ直下に以下のファイルを作成
- ファイル名は任意

内容
```shell
$ vi ~/.gitconfig_github_[アカウント名]
```
実例
```shell
$ vi ~/.gitconfig_github_account1
```
内容
```shell
[user]
	name = 追加するアカウントのユーザー名
	email = 追加するアカウントのメールアドレス
```

### `.gitconfig` ファイルを修正
- `gitdir:` 追加するアカウントのディレクトリを指定
- `path` 追加するアカウント用の`.gitconfig` ファイルのパスを指定

内容
```shell
[includeIf "gitdir:~/追加するアカウントを適用するディレクトリ"]
	path = 追加するアカウント用の設定ファイル名のパス
```

実例
```shell
[includeIf "gitdir:~/source/github/account1"]
	path = ~/.gitconfig_github_account1
```

## 6.GitHubアカウントが自動的に切り替わることを確認

```shell
$ mkdir -p ~/source/github/account1 && cd $_
$ pwd
$ git init
$ git config user.name
# 追加するアカウントのユーザー名が表示されればOK
$ git config user.email
# 追加するアカウントのメールアドレスが表示されればOK
```


## 7. リモートリポジトリの追加

### 通常の場合
{{< hint danger >}}
注意：今回 こちらは実施しないこと
{{</hint>}}
内容
```shell
git remote add origin git@github.com:[アカウント名]/[リポジトリ名].git
```
実例
```shell
git remote add origin git@github.com:account1/repository1.git
```

### 追加の場合
{{< hint danger >}}
注意：今回 こちらを実施すること
{{</hint>}}
内容
```shell
git remote add origin [PC内で切り分けるためのホスト名]:[追加するアカウント名]/[リポジトリ名].git
```
実例
```shell
git remote add origin github.account1:account1/repository1.git
```

## 8. 同じ環境の別PCのローカルにクローンする場合
内容
```shell
$ git clone git@github.com:[アカウント名]/[リポジトリ名].git
$ cd [リポジトリ名]
$ git remote -v
origin  git@github.com:[アカウント]/[リポジトリ名].git (fetch)
origin  git@github.com:[アカウント名]/[リポジトリ名].git (push)
$ git remote rm origin
$ git remote add origin [PC内で切り分けるためのホスト名]:[アカウント名]/[リポジトリ名].git
$ git remote -v
origin  [PC内で切り分けるためのホスト名]:[アカウント名]/[リポジトリ名].git.git (fetch)
origin  [PC内で切り分けるためのホスト名]:[アカウント名]/[リポジトリ名].git.git (push)
```
実例
```shell
$ git clone git@github.com:account1/repository1.git
$ cd repository1
$ git remote -v
origin  git@github.com:account1/repository1.git (fetch)
origin  git@github.com:account1/repository1.git (push)
$ git remote rm origin
$ git remote add origin github.account1:account1/repository1.git
$ git remote -v
origin  github.account1:account1/repository1.git (fetch)
origin  github.account1:account1/repository1.git (push)
# 上記のように origin の Host が切り替わっていればOK
```

## 他のGitHub アカウントを追加
- 上記の手順を実施
  - その際 `account1` の箇所を別な任意の名称に変更して置換すること

## References
- [GitHub Docs 新しいSSHキーを生成して ssh-agent に追加する](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [ssh-keygen コマンド SSHの公開鍵と秘密鍵を作成する](https://atmarkit.itmedia.co.jp/ait/articles/1908/02/news015.html)
- [複数のGitHubアカウントを1つのPCのディレクトリで使い分ける方法](https://tanacio.com/how-to-use-multiple-github-accounts/)