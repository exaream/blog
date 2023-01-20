---
date: 2023-01-19
lastmod: ["lastmod"]
title: Multiple GitHub Account on 1 PC
linkTitle: Multiple GitHub Account on 1 PC
description: Multiple GitHub Account on 1 PC
tags: ["GitHub","Multiple Account","Configuration"]
---

# 複数の GitHub アカウントを1つのPCで使用

## 概要
- 複数のGitHubアカウントを1つのPCのディレクトリで分けて使用する方法
- 今回は1つのPCにあるディレクトリ `foo` と `bar` に個別のGitHubアカウントを設定


## 1. 公開鍵と秘密鍵を作成

- まず `foo` 用の公開鍵と秘密鍵を作成

### `ssh-keygen` のオプション
|option|説明|備考|
|---|---|---|
|`-t`|鍵の暗号化方式|今回は強度の高い `ed25519` を選択|
|`-P`|パスワード|今回は空を指定|
|`-C`|任意のコメント|今回はGitHubのメールアドレスを指定|
|`-f`|出力するファイル名|今回は `id_暗号化方式_任意の識別子` を指定|

### `ssh-keygen` で作成
```shell
$ cd ~/.ssh

$ ssh-keygen --help

$ ssh-keygen -t ed25519 -P '' -C 'GitHubのメールアドレス' -f id_ed25519_foo

$ ls -l
config
id_ed25519_foo     # foo用の秘密鍵
id_ed25519_foo.pub # foo用の公開鍵
known_hosts
```

### 公開鍵の内容をコピー
Windows
```shell
clip < ~/.ssh/id_ed25519_foo.pub
```
Mac
```shell
pbcopy < ~/.ssh/id_ed25519_foo.pub
or
cat ~/.ssh/id_ed25519_foo.pub | pbcopy
```

## 2. GitHub アカウントに公開鍵を設定

1. `foo` 用のGitHubアカウントでログイン https://github.com/login
1. SSHキーの設定画面にアクセス https://github.com/settings/ssh
1. `New SSH key` 押下
|項目|内容|
|---|---|
|Title|任意の値を入力(識別できればOK)|
|Key Type|Authentication Key|
|Key|コピーした公開鍵の内容を貼り付け|
1. `Add SSH Key` 押下

## 3. SSH設定ファイルに GitHub アカウント を追加

```shell
touch ~/.ssh/config
```

```shell
Host github.foo
	HostName github.com
	IdentityFile ~/.ssh/id_ed25519_foo
	User git
	IdentitiesOnly yes
	Port 22
	TCPKeepAlive yes
```

## 4. GitHub アカウントと SSH 接続ができているか確認

- 以下のように表示されていればOK
```shell
$ ssh -T github.foo
Hi foo用のGitHubアカウント名! You\'ve successfully authenticated, but GitHub does not provide shell access.
```

## 5. ディレクトリごとに GitHub アカウントが切り替わるよう設定

### 追加する GitHub アカウント用の `.gitconfig` ファイルを作成

- ホームディレクトリ直下に以下のファイルを作成
- ファイル名は任意
```shell
$ touch ~/.gitconfig_foo
```
```shell
[user]
	name = GitHubアカウントのユーザー名
	email = GitHubアカウントのメールアドレス
```

### `.gitconfig` ファイルを修正
- `gitdir:` に追加する GitHub アカウントのディレクトリを指定
- `path` に追加する GitHub アカウント用の`.gitconfig` ファイルのパスを指定
```shell
[includeIf "gitdir:~/Workspaces/foo"]
	path = ~/.gitconfig_foo
```

## 6. GitHub アカウントが自動的に切り替わることを確認

```shell
$ cd ~/Workspaces/foo
$ git init
$ git config user.name
# 追加した GitHub アカウントのユーザー名が表示されればOK
$ git config user.email
# 追加した GitHub アカウントのメールアドレスが表示されればOK

```


## 7. 他のGitHub アカウントを追加
- 上記の手順 1〜6 を実施
  - その際 `foo` の箇所を別な任意の名称に変更して置換すること

## 補足

### リモートリポジトリの追加方法

通常
```shell
git remote add origin git@github.com:GitHubアカウントのユーザ名/リポジトリ名.git
```
追加分
```shell
git remote add origin [configファイルで設定したHost]:[追加したGitHubアカウントのユーザ名]/[リポジトリ名].git
```
```shell
git remote add origin github.foo:exaream/sample.git
```

### 同じ環境の別PCのローカルにクローンする場合

```shell
git clone git@github.com:exaream/sample.git
cd sample
git remote rm origin
git remote add origin github.foo:exaream/sample.git
```

- 以下のように `git clone` 後に `git remote add` した場合、以下のエラーが発生
```text
error: remote origin already exists.
```
- `git remote -v` で確認するとすでに存在しているため
```shell
git clone git@github.com:exaream/sample.git
cd practice
git remote add origin github.foo:exaream/sample.git
error: remote origin already exists.
git remote -v
origin  git@github.com:exaream/sample.git (fetch)
origin  git@github.com:exaream/sample.git (push)
```

## References
- [GitHub Docs 新しい SSH キーを生成して ssh-agent に追加する](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [ssh-keygen コマンド SSHの公開鍵と秘密鍵を作成する](https://atmarkit.itmedia.co.jp/ait/articles/1908/02/news015.html)
- [複数のGitHubアカウントを1つのPCのディレクトリで使い分ける方法](https://tanacio.com/how-to-use-multiple-github-accounts/)