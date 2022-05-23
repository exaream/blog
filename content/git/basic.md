---
date: 2020-01-01
lastmod: ["lastmod"]
title: Git Basic
linkTitle: Basic
description: Git Basic
tags: ["Git","Log","Configuration"]
---

# Git Basic

## 実用的なコマンド

### 頻繁に使うコマンド

```shell
$ git branch -a
$ git checkout -b sample_branch_name
# Edit source code
$ git status
$ git add .
$ git commit -m "message"
$ git push origin sample_branch_name
```

```shell
git tag sample_tag_name
git tag -n
git push origin sample_tag_name
```

### 指定したフォーマットでコミット・ログを表示

```shell
$ git log --graph --date=iso --pretty=format:"[%ad] %h %an : %s"
```

### 特定の注釈を含むコミットを検索

```shell
$ git log --oneline --grep "<pattern>"
```

### コミット間の変更ファイル一覧を表示

```shell
$ git diff --stat <Commit A> <Commit B>
```
ファイル名のみ表示する場合
```shell
$ git diff --stat --name-only <Commit A> <Commit B>
```

## Git 設定

### バージョンを確認

```shell
$ git --version
```

### 設定の一覧を表示

設定適用範囲
* system: システム全体に適用
* global: ユーザー単位で適用（ユーザーのホームディレクトリ直下の設定ファイルを参照）
* local: Gitリポジトリ単位で適用（```.git``` ディレクトリ直下の設定ファイルを参照）
```shell
$ git config --system --list
$ git config --global --list
$ git config --local --list
```

適用範囲を指定しない場合 ```--local``` と同様に Gitリポジトリ内の設定を表示
```shell
$ git config --list
```
Gitリポジトリの管理するディレクトリ外で以下を実行した場合 エラーが発生
```shell
$ git config --local --list
fatal: --local can only be used inside a git repository
```

```--list``` オプションの短縮版
```shell
$ git config --system -l
$ git config --global -l
$ git config --local -l
```

### 設定ファイルを直接編集

```shell
$ git config --system --edit
$ git config --global --edit
$ git config --local --edit
```
```--edit``` オプションの短縮版
```shell
$ git config --system -e
$ git config --global -e
$ git config --local -e
```

### どの設定ファイルに記載された設定が適用されているか確認

```shell
$ git config --show-origin user.name
$ git config --show-origin user.email
$ git config --show-origin core.autocrlf
```

### ユーザー名/メールアドレスを設定

* --global オプションを付けないと、該当のリポジトリのみ有効な設定になる
```shell
$ git config --global user.name <username>
$ git config --global user.email <mailaddress>
```

### 出力をカラーリングするよう設定

```shell
$ git config --global color.ui auto
```

### コマンドにエイリアスを設定

```shell
$ git config --global alias.<aliasname> <commandname>
```

### 不要なファイルを管理対象外

* ```.gitignore``` に記載されたファイル名は、Gitの管理対象外
* ```.gitignore``` ファイル自体をコミットしておくこと
```shell
$ echo <file name> >> .gitignore
```
### 空ディレクトリを管理対象

* Gitでは空ディレクトリは管理対象外
* 空ディレクトリを管理対象にする場合、任意のファイルを配置
* 慣例的に ```.gitkeep``` というファイルを使用
```shell
$ cd <directory name>
$ touch .gitkeep
```

### プロキシサーバを経由してhttp接続

```shell
$ git config --global http.proxy <プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```
* ```.gitconfig``` ファイルの http の項目に次の設定を直接追加することもできる
```vim
[http]
proxy = <プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```

### ユーザー認証の必要なプロキシサーバを経由してhttp接続

```shell
$ git config --global http.proxy http://<ユーザ名ー>:<パスワード>@<プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```
* ```.gitconfig``` ファイルの http の項目に次の設定を直接追加することもできる
```vim
[http]
proxy = http://<ユーザー名>:<パスワード>@<プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```

## 基本操作

### リポジトリを作成

```shell
$ git init
```

### ファイルやディレクトリをインデックスに登録

```shell
$ git add .
```

```shell
$ git add *.txt
```

### インデックスに追加されたファイルをコミット

```shell
$ git commit
```

### 変更されたファイルの一覧を表示

```shell
$ git status
```

### 変更されたファイルの差分を表示

```shell
$ git diff
```

### コミットログを表示

```shell
$ git log
```

### コミットログを表示

```git log``` コマンドで参照したコミットやHEADを指定
```shell
$ git show <commit>
```

### ファイルやディレクトリ名の変更や移動

```shell
$ git mv <old file name> <new file name>
```

### 管理対象ではないファイルを削除

* -n ： 削除されるファイルを確認
* -f ： 実際にファイルを削除
* -x ： .gitignore に指定されたファイルも削除
```shell
$ git clean
```

### 手元で変更したインデックスに登録されていないファイルを元に戻す

```shell
$ git checkout -- <file>
```

### インデックスに登録したファイルの取り消し

```shell
$ git reset HEAD -- <file>
```

### 以前にコミットしたことのあるファイルだけを全てインデックスに登録

```shell
$ git add -u
```

## コミットログ操作

### コミットログを表示

```shell
$ git log
```

各コミットログを1行で表示
```shell
$ git log --oneline
```

#### ```git log``` の主なオプション

|オプション|内容|
|---|---|
|-n|件数を指定して表示|
|--relative-date|相対フォーマット (“3 days ago” など) で日付を表示|
|--oneline|1行で表示|
|--pretty|フォーマットを指定して表示. オプションは oneline, short, full, fuller, fomat(独自指定)|
|--graph|ブランチやマージの履歴をアスキーグラフで表示|

#### ```git log --pretty``` のフォーマット

|置換文字列|内容|
|---|---|
|%H|コミットのハッシュ|
|%h|コミットのハッシュ (短縮版)|
|%T|ツリーのハッシュ|
|%t|ツリーのハッシュ (短縮版)|
|%P|親のハッシュ|
|%p|親のハッシュ (短縮版)|
|%an|Author の名前|
|%ae|Author のメールアドレス|
|%ad|Author の日付 (-date= オプションに従った形式)|
|%ar|Author の相対日付|
|%cn|Committer の名前|
|%ce|Committer のメールアドレス|
|%cd|Committer の日付|
|%cr|Committer の相対日付|
|%s|件名|

#### ```git log``` の日付フォーマット

|フォーマット|内容|
|---|---|
|relative|相対時間 (3 days ago)|
|local|ローカルタイムゾーン|
|iso|ISO 8601 フォーマット|
|rfc|RFC 2822 フォーマット|
|short|YYYY-MM-DD|
|raw|%s %z|
|default|標準|

### 直前のコミットの内容を修正

現在のブランチの先頭のコミットに上書き
入力画面が表示されるのでコミットログを確認して保存
```shell
$ git commit --amend
```
入力画面が表示されず即上書き保存
```shell
git commit --amend --no-edit
```

### 直前のコミットのコメントだけを修正

インデックスにファイルを登録していない状態で再コミット  
入力画面が表示されるのでコメントを修正して保存

```shell
$ git commit --amend
```

### 過去のコミットの内容を修正

1. 指定したコミットより後のコミットを指定すると、コミットの一覧が表示される
2. その中から修正したいコミットを見つけてその行の ```pick``` の文字を ```edit``` に変更して保存・終了する

```shell
$ git rebase -i <commit>
```

3. 修正したいファイルを編集、保存した後に ```--amend``` オプションを指定してコミット
```shell
$ git commit --amend
```

4. ```--continue``` オプションを指定し ```rebase``` を実行
```shell
$ git rebase --continue
```

### 過去のコミットのコメントを修正

1. 指定したコミットより後のコミットを指定すると、コミットの一覧が表示される
2. その中から修正したいコミットを見つけてその行の ```pick``` の文字を ```edit``` に変更して保存・終了する

```shell
$ git rebase -i <commit>
```

3. 修正したいファイルを編集、保存した後に ```--amend``` オプションを指定してコミット
4. コメントの入力画面が表示されるので、コメントを修正
```shell
$ git commit --amend
```

5. ```--continue``` オプションを指定し ```rebase``` を実行
```shell
$ git rebase --continue
```

### ```rebase``` を途中で中止

```shell
$ git rebase --abort
```

### HEADの移動履歴を表示

過去に HEAD が指していたコミットの一覧を表示(削除したコミットや rebase 等によってまとめられたコミットなども表示)
```shell
$ git reflog
```

### ブランチの先頭の移動履歴

過去にそのブランチの先頭が指していたコミットの一覧

```shell
$ git reflog <branch name>
```

### 直前のコミットを取り消し

```shell
$ git reset --hard HEAD~
```

### ```rebase``` を取り消し

1. HEADの移動履歴から ```rebase``` 直前のコミットを確認
```shell
$ git reflog
```
2. 見つけたハッシュ値を ```<commit>``` で指定して ```reset``` を実行
```shell
$ git reset --hard <commit>
```

### 直前の ```reset``` を取り消し

```reset``` 前のコミットは ```ORIG_HEAD``` という名前で参照できるので、これを指定して ```reset``` を実行

```shell
$ git reset --hard ORIG_HEAD
```

### ブランチを間違えてしまったコミットを移動

```<commit>``` で指定したコミットを現在のブランチにコピー

```shell
$ git cherry-pick "<commit>"
```

### 特定のコメントを含むコミットを検索

```<pattern>``` で指定した文字がコミットログに含まれるコミット

```shell
$ git log --grep "<pattern>"
```

## ブランチ操作

### ローカル・ブランチの一覧を表示

```shell
$ git branch
```

### 特定の文字列を含むブランチをローカル・ブランチから検索

```shell
$ git branch --list 'sample-*'
```
or
```shell
$ git branch -l 'sample-*'
```

### ローカル・ブランチとリモート・ブランチの一覧を表示

```shell
$ git branch --all
```
or
```shell
$ git branch -a
```

### 特定の文字列を含むブランチをローカル・ブランチとリモート・ブランチから検索

```shell
$ git branch --all --list 'sample-*'
```
or
```shell
$ git branch -a -l 'sample-*'
```

### ブランチを作成

```shell
$ git branch <branch name>
```

### ブランチ名を変更

```shell
$ git branch -m <old branch> <new branch>
```

### ブランチを削除

```shell
$ git branch -d <branch name>
```

### ブランチを強制的に削除

```HEAD``` にマージされていないコミットがあるブランチを強制的に削除

```shell
$ git branch -D <branch name>
```


### ブランチを切り替え

```shell
$ git checkout <branch>
```

ブランチの作成と切り替えを一度に実行
```shell
$ git checkout -b <branch>
```

### ブランチをマージ

```shell
$ git merge <branch>
```

fast-forwardなマージであってもマージコミットを作成  
ブランチが存在したという情報を残したいときに有用
```shell
$ git merge --no-ff <branch>
```

## リモート操作

### 既存のリモートリポジトリを複製

```shell
$ git clone <repository url>
```

### リモートリポジトリを追加

```shell
$ git remote add <name> <repository url>
```


### リモートリポジトリの一覧を表示

```shell
$ git remote
```

### リモートリポジトリのブランチからローカルリポジトリのブランチを作成

```shell
$ git checkout <branch>
```

### リモートリポジトリにブランチを作成 / ブランチに変更内容を反映

* ```repository``` や ```refspec``` は省略可
* ```repository``` には ```remote add``` コマンドで追加したリポジトリ名以外にURLを直接指定可
* ```repository``` を省略した場合, 追跡しているリモートリポジトリを指定
* ```refspec``` はブランチ名などを指定可
* ```refspec``` を省略した場合, デフォルトでは全てのブランチが対象

```shell
$ git push <repository> <refspec>
```

### リモートリポジトリのブランチの変更内容を確認

* リモートリポジトリでの変更内容を確認したいがローカルリポジトリには反映させたくない場合に ```fetch```を実行
* ```repository``` や ```refspec``` は省略可
* ```repository``` には ```remote add``` コマンドで追加したリポジトリ名以外にURLを直接指定可
* ```repository``` を省略した場合, 追跡しているリモートリポジトリを指定
* ```refspec``` はブランチ名などを指定可
* ```refspec``` を省略した場合, デフォルトでは全てのブランチが対象


```shell
$ git fetch <repository> <refspec>
```

### リモートリポジトリから削除されたブランチをローカルリポジトリでも削除

```shell
$ git fetch --prune
```
or
```shell
$ git remote prune <repository>
```


### リモートリポジトリのブランチの変更内容をローカルリポジトリのブランチに反映

* ```pull = fetch + merge```
* ```repository``` や ```refspec``` は省略可
* ```repository``` には ```remote add``` コマンドで追加したリポジトリ名以外にURLを直接指定可
* ```repository``` を省略した場合, 追跡しているリモートリポジトリを指定
* ```refspec``` はブランチ名などを指定可
* ```refspec``` を省略した場合, デフォルトでは全てのブランチが対象

```shell
$ git pull <repository> <refspec>
```

### リモートリポジトリのブランチを削除

```shell
$ git push --delete <repository> <branch name>
```

### リモートリポジトリにタグを作成

```shell
$ git push <repository> <tag name>
```

### リモートリポジトリのタグを削除

```shell
$ git push --delete <repository> <tag name>
```

### 登録済のリモートリポジトリのアドレスを変更

指定した名前で登録されているリモートリポジトリのアドレスを <newurl> のアドレスに変更

```shell
$ git remote set-url <name> <newurl>
```

### 登録済のリモートリポジトリの名前を変更

```shell
$ git remote rename <old> <new>
```

## サブモジュール(submodule)操作

### サブモジュールの設定
```.gitmodules``` というファイルを作成して以下の内容を記述
```shell
[submodule "<submodule name>"]
	path = <directory name>
	url = <repository url>
```

e.g.)
```shell
[submodule "themes/hugo-book"]
	path = themes/hugo-book
	url = https://github.com/alex-shpak/hugo-book
```

### サブモジュールを含めて既存のリモートリポジトリを複製
```shell
$ git clone --recursive <repository url>
```

### サブモジュールを後から複製
```shell
$ git submodule update --init --recursive
```

### サブモジュールのリモートリポジトリのブランチを指定し最新を反映
```shell
$ git submodule add -b <branch name> <repository url>
```

### サブモジュールのリモートリポジトリのマスタの最新を反映
```shell
git submodule update --remote
```

## タグ操作

### ローカルのタグの一覧を表示

タグ名のみ
```shell
$ git tag
```

タグ名と注釈

```shell
$ git tag -n
```

* ```-n``` 表示する注釈の行数
```shell
$ git tag -n1
```

### リモートのタグの一覧を表示

リモート・リポジトリ ```origin``` のタグの一覧を取得
```shell
$ git ls-remote --tags origin
```

### リモートのタグを取得 ```fetch```

```shell
$ git fetch --all --tags
```

### タグ付け (タグを作成)

#### 現在のコミットにタグ付け

注釈なし
```shell
$ git tag <tag name>
```

注釈付き
```shell
$ git tag -m "<tag message>" <tag name>
$ git tag --message "<tag message>" <tag name>
```

注釈付き (viが起動)
```shell
$ git tag -a <tag name>
$ git tag --annotate <tag name>
```

#### 後からコミットにタグ付け

指定したコミット, 注釈付き
```shell
$ git tag -m "<tag message>" <tag name> <commit>
$ git tag --message "<tag message>" <tag name> <commit>
```

指定したコミット, 注釈付き (viが起動)
```shell
$ git tag -a <tag name> -m <tag message> <commit>
$ git tag --annotate <tag name> --message <tag message> <commit>
```

### タグを削除

まだプッシュしていないタグは削除可
```shell
$ git tag -d <tag name>
$ git tag --delete <tag name>
```

### タグを共有

リモート・リポジトリ ```origin``` にプッシュ
```shell
$ git push origin <tag name>
```

全てのタグをリモート・リポジトリ ```origin``` にプッシュ
```shell
$ git push origin --tags
```

### タグをチェックアウト

```shell
$ git checkout <tag name>
```

タグをチェックアウトすると同時にブランチを作成
```shell
$ git checkout <tag name> -b <branch name>
```

## トラブル対応

### Error "would clobber existing tag"
リモートとローカルのリポジトリのタグ名が重複していることが原因  
リモート・リポジトリのタグでローカル・リポジトリのタグを上書きし解消
```shell
$ git fetch --tags -f
```

### 過去のコミットの編集者(Author)とコミッター(Committer)を変更

編集者(Author)とコミッター(Committer)のユーザー名とメールを確認
```shell
$ git log --pretty=full
commit XXXXXXXXXX (HEAD -> sample-branch, origin/sample-branch)
Author: before-foo <before-foo@sample.com>
Commit: before-bar <before-bar@sample.com>
```

過去の全コミットを変更
```shell
 $ git filter-branch -f --env-filter \
     "GIT_AUTHOR_NAME='after-foo'; \
     GIT_AUTHOR_EMAIL='after-foo@sample.com'; \
     GIT_COMMITTER_NAME='after-bar'; \
     GIT_COMMITTER_EMAIL='after-bar@sample.com';" \
   HEAD
```

過去のコミットに反映されたことを確認
```shell
$ git log --pretty=full
```

リモートリポジトリへ強制的に反映
```shell
git push -f
```

特定の条件の過去のコミットのみ修正する場合
```shell
$ git filter-branch --commit-filter ' 
    if [ "$GIT_COMMITTER_EMAIL" = "before-bar@sample.com" ];
      then
        GIT_AUTHOR_NAME="after-foo";
        GIT_AUTHOR_EMAIL="after-foo@sample.com";
        GIT_COMMITTER_NAME="after-bar";
        GIT_COMMITTER_EMAIL="after-bar@sample.com";
        git commit-tree "$@";
    else
        git commit-tree "$@";
    fi'  HEAD
```

## その他
### 特定のディレクトリやファイルのみチェックアウト
```shell
mkdir work && cd $_
git init .
git sparse-checkout init --cone
git sparse-checkout set <target path of directory or file>
git sparse-checkout list
git remote add origin <remote url>
git pull origin <branch name>
ls -al <target path of directory or file>
```

### HEAD~ と HEAD^ の違い
* ^ (キャレット): n番目の親
* ~ (チルダ): 最初の親のみに続くn世代前の祖先

https://git-scm.com/docs/git-rev-parse#_specifying_revisions

```
G   H   I   J
 \ /     \ /
  D   E   F
   \  |  / \
    \ | /   |
     \|/    |
      B     C
       \   /
        \ /
         A
```
```
A =      = A^0
B = A^   = A^1     = A~1
C =      = A^2
D = A^^  = A^1^1   = A~2
E = B^2  = A^^2
F = B^3  = A^^3
G = A^^^ = A^1^1^1 = A~3
H = D^2  = B^^2    = A^^^2  = A~2^2
I = F^   = B^3^    = A^^3^
J = F^2  = B^3^2   = A^^3^2
```

## バージョンアップ

### macOS

```shell
$ git --version
$ brew ls | grep git
nothing
$ brew install git
$ brew link --overwrite git
$ which git
/usr/local/bin/git
$ ls -la /usr/local/bin/git
$ git --version
```

## Security

### git-secrets
- https://github.com/awslabs/git-secrets
- https://blog.katsubemakito.net/git/git-secrets

#### Install
macOS
```shell
$ brew install git-secrets
```

```shell
$ git clone https://github.com/awslabs/git-secrets.git
$ cd git-secrets
$ make install
```

#### Basic usage
Set git-secrets to a git repository
```shell
$ git init
$ git secrets --install
```
Check commit history
```shell
$ git secrets --scan-history
```
For AWS
```shell
$ git secrets --register-aws
```
## References
* https://qiita.com/gold-kou/items/7f6a3b46e2781b0dd4a0
* https://qiita.com/jesus_isao/items/63557eba36819faa4ad9
* https://backlog.com/ja/git-tutorial/reference/
* https://qiita.com/harukasan/items/9149542584385e8dea75
* https://qiita.com/kawasaki_dev/items/41afaafe477b877b5b73
* [GitのCommitユーザを修正する方法](https://qiita.com/y10exxx/items/dcea0e39788d649ca8ba)
