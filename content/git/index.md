---
date: 2020-01-01
lastmod: ["lastmod"]
title: Git
tags: ["Git","Log","Configuration","Memo"]
---

# Git Memo

## 実用的なコマンド

### 指定したフォーマットでコミット・ログを表示

```bash
$ git log --graph --date=iso --pretty=format:"[%ad] %h %an : %s"
```

### 特定の注釈を含むコミットを検索

```bash
$ git log --oneline --grep "<pattern>"
```

### コミット間の変更ファイル一覧を表示

```bash
$ git diff --stat <Commit A> <Commit B>
```
ファイル名のみ表示する場合
```bash
$ git diff --stat --name-only <Commit A> <Commit B>
```
### タグの一覧を表示

```bash
$ git tag -n
```

## Git 設定

### バージョンを確認

```bash
$ git --version
```

### 設定の一覧を表示

設定適用範囲
* system: システム全体に適用
* global: ユーザー単位で適用（ユーザーのホームディレクトリ直下の設定ファイルを参照）
* local: Gitリポジトリ単位で適用（```.git``` ディレクトリ直下の設定ファイルを参照）
```bash
$ git config --system --list
$ git config --global --list
$ git config --local --list
```

適用範囲を指定しない場合 ```--local``` と同様に Gitリポジトリ内の設定を表示
```bash
$ git config --list
```
Gitリポジトリの管理するディレクトリ外で以下を実行した場合 エラーが発生
```bash
$ git config --local --list
fatal: --local can only be used inside a git repository
```

```--list``` オプションの短縮版
```bash
$ git config --system -l
$ git config --global -l
$ git config --local -l
```

### 設定ファイルを直接編集

```bash
$ git config --system --edit
$ git config --global --edit
$ git config --local --edit
```
```--edit``` オプションの短縮版
```bash
$ git config --system -e
$ git config --global -e
$ git config --local -e
```

### どの設定ファイルに記載された設定が適用されているか確認

```bash
$ git config --show-origin user.name
$ git config --show-origin user.email
$ git config --show-origin core.autocrlf
```

### ユーザー名/メールアドレスを設定

* --global オプションを付けないと、該当のリポジトリのみ有効な設定になる
```bash
$ git config --global user.name <username>
$ git config --global user.email <mailaddress>
```

### 出力をカラーリングするよう設定

```bash
$ git config --global color.ui auto
```

### コマンドにエイリアスを設定

```bash
$ git config --global alias.<aliasname> <commandname>
```

### 不要なファイルを管理対象外

* ```.gitignore``` に記載されたファイル名は、Gitの管理対象外
* ```.gitignore``` ファイル自体をコミットしておくこと
```bash
$ echo <file name> >> .gitignore
```
### 空ディレクトリを管理対象

* Gitでは空ディレクトリは管理対象外
* 空ディレクトリを管理対象にする場合、任意のファイルを配置
* 慣例的に ```.gitkeep``` というファイルを使用
```bash
$ cd <directory name>
$ touch .gitkeep
```

### プロキシサーバを経由してhttp接続

```bash
$ git config --global http.proxy <プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```
* ```.gitconfig``` ファイルの http の項目に次の設定を直接追加することもできる
```vim
[http]
proxy = <プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```

### ユーザ認証の必要なプロキシサーバを経由してhttp接続

```bash
$ git config --global http.proxy http://<ユーザ名>:<パスワード>@<プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```
* ```.gitconfig``` ファイルの http の項目に次の設定を直接追加することもできる
```vim
[http]
proxy = http://<ユーザ名>:<パスワード>@<プロキシサーバのアドレス>:<プロキシサーバのポート番号>
```

## 基本操作

### リポジトリを作成

```bash
$ git init
```

### ファイルやディレクトリをインデックスに登録

```bash
$ git add .
```

```bash
$ git add *.txt
```

### インデックスに追加されたファイルをコミット

```bash
$ git commit
```

### 変更されたファイルの一覧を表示

```bash
$ git status
```

### 変更されたファイルの差分を表示

```bash
$ git diff
```

### コミットログを表示

```bash
$ git log
```

### コミットログを表示

```git log``` コマンドで参照したコミットやHEADを指定
```bash
$ git show <commit>
```

### ファイルやディレクトリ名の変更や移動

```bash
$ git mv <old file name> <new file name>
```

### 管理対象ではないファイルを削除

* -n ： 削除されるファイルを確認
* -f ： 実際にファイルを削除
* -x ： .gitignore に指定されたファイルも削除
```bash
$ git clean
```

### 手元で変更したインデックスに登録されていないファイルを元に戻す

```bash
$ git checkout -- <file>
```

### インデックスに登録したファイルの取り消し

```bash
$ git reset HEAD -- <file>
```

### 以前にコミットしたことのあるファイルだけをすべてインデックスに登録

```bash
$ git add -u
```

## コミットログ操作

### コミットログを表示

```bash
$ git log
```

各コミットログを1行で表示
```bash
$ git log --oneline
```

#### ```git log``` のオプション

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
```bash
$ git commit --amend
```
入力画面が表示されず即上書き保存
```bash
git commit --amend --no-edit
```

### 直前のコミットのコメントだけを修正

インデックスにファイルを登録していない状態で再コミット  
入力画面が表示されるのでコメントを修正して保存

```bash
$ git commit --amend
```

### 過去のコミットの内容を修正

1. 指定したコミットより後のコミットを指定すると、コミットの一覧が表示される
2. その中から修正したいコミットを見つけてその行の ```pick``` の文字を ```edit``` に変更して保存・終了する

```bash
$ git rebase -i <commit>
```

3. 修正したいファイルを編集、保存した後に ```--amend``` オプションを指定してコミット
```bash
$ git commit --amend
```

4. ```--continue``` オプションを指定し ```rebase``` を実行
```bash
$ git rebase --continue
```

### 過去のコミットのコメントを修正

1. 指定したコミットより後のコミットを指定すると、コミットの一覧が表示される
2. その中から修正したいコミットを見つけてその行の ```pick``` の文字を ```edit``` に変更して保存・終了する

```bash
$ git rebase -i <commit>
```

3. 修正したいファイルを編集、保存した後に ```--amend``` オプションを指定してコミット
4. コメントの入力画面が表示されるので、コメントを修正
```bash
$ git commit --amend
```

5. ```--continue``` オプションを指定し ```rebase``` を実行
```bash
$ git rebase --continue
```

### ```rebase``` を途中で中止

```bash
$ git rebase --abort
```

### HEADの移動履歴を表示

過去に HEAD が指していたコミットの一覧を表示(削除したコミットや rebase 等によってまとめられたコミットなども表示)
```bash
$ git reflog
```

### ブランチの先頭の移動履歴

過去にそのブランチの先頭が指していたコミットの一覧

```bash
$ git reflog <branch name>
```

### 直前のコミットを取り消し

```bash
$ git reset --hard HEAD~
```

### ```rebase``` を取り消し

1. HEADの移動履歴から ```rebase``` 直前のコミットを確認
```bash
$ git reflog
```
2. 見つけたハッシュ値を ```<commit>``` で指定して ```reset``` を実行
```bash
$ git reset --hard <commit>
```

### 直前の ```reset``` を取り消し

```reset``` 前のコミットは ```ORIG_HEAD``` という名前で参照できるので、これを指定して ```reset``` を実行

```bash
$ git reset --hard ORIG_HEAD
```

### ブランチを間違えてしまったコミットを移動

```<commit>``` で指定したコミットを現在のブランチにコピー

```bash
$ git cherry-pick "<commit>"
```

### 特定のコメントを含むコミットを検索

```<pattern>``` で指定した文字がコミットログに含まれるコミット

```bash
$ git log --grep "<pattern>"
```

## ブランチ操作

### ローカル・ブランチの一覧を表示

```bash
$ git branch
```

### 特定の文字列を含むブランチをローカル・ブランチから検索

```bash
$ git branch --list 'sample-*'
```
or
```bash
$ git branch -l 'sample-*'
```

### ローカル・ブランチとリモート・ブランチの一覧を表示

```bash
$ git branch --all
```
or
```bash
$ git branch -a
```

### 特定の文字列を含むブランチをローカル・ブランチとリモート・ブランチから検索

```bash
$ git branch --all --list 'sample-*'
```
or
```bash
$ git branch -a -l 'sample-*'
```

### ブランチを作成

```bash
$ git branch <branch name>
```

### ブランチ名を変更

```bash
$ git branch -m <old branch> <new branch>
```

### ブランチを削除

```bash
$ git branch -d <branch name>
```

### ブランチを強制的に削除

```HEAD``` にマージされていないコミットがあるブランチを強制的に削除

```bash
$ git branch -D <branch name>
```


### ブランチを切り替え

```bash
$ git checkout <branch>
```

ブランチの作成と切り替えを一度に実行
```bash
$ git checkout -b <branch>
```

### ブランチをマージ

```bash
$ git merge <branch>
```

fast-forwardなマージであってもマージコミットを作成  
ブランチが存在したという情報を残したいときに有用
```bash
$ git merge --no-ff <branch>
```

## リモート操作

### 既存のリモートリポジトリを複製

```bash
$ git clone <url>
```

### リモートリポジトリを追加

```bash
$ git remote add <name> <url>
```


### リモートリポジトリの一覧を表示

```bash
$ git remote
```

### リモートリポジトリのブランチからローカルリポジトリのブランチを作成

```bash
$ git checkout <branch>
```

### リモートリポジトリにブランチを作成 / ブランチに変更内容を反映

* ```repository``` や ```refspec``` は省略可
* ```repository``` には ```remote add``` コマンドで追加したリポジトリ名以外にURLを直接指定可
* ```repository``` を省略した場合, 追跡しているリモートリポジトリを指定
* ```refspec``` はブランチ名などを指定可
* ```refspec``` を省略した場合, デフォルトではすべてのブランチが対象

```bash
$ git push <repository> <refspec>
```

### リモートリポジトリのブランチの変更内容を確認

* リモートリポジトリでの変更内容を確認したいがローカルリポジトリには反映させたくない場合に ```fetch```を実行
* ```repository``` や ```refspec``` は省略可
* ```repository``` には ```remote add``` コマンドで追加したリポジトリ名以外にURLを直接指定可
* ```repository``` を省略した場合, 追跡しているリモートリポジトリを指定
* ```refspec``` はブランチ名などを指定可
* ```refspec``` を省略した場合, デフォルトではすべてのブランチが対象


```bash
$ git fetch <repository> <refspec>
```

### リモートリポジトリから削除されたブランチをローカルリポジトリでも削除

```bash
$ git fetch --prune
```
or
```bash
$ git remote prune <repository>
```


### リモートリポジトリのブランチの変更内容をローカルリポジトリのブランチに反映

* ```pull = fetch + merge```
* ```repository``` や ```refspec``` は省略可
* ```repository``` には ```remote add``` コマンドで追加したリポジトリ名以外にURLを直接指定可
* ```repository``` を省略した場合, 追跡しているリモートリポジトリを指定
* ```refspec``` はブランチ名などを指定可
* ```refspec``` を省略した場合, デフォルトではすべてのブランチが対象

```bash
$ git pull <repository> <refspec>
```

### リモートリポジトリのブランチを削除

```bash
$ git push --delete <repository> <branch name>
```

### リモートリポジトリにタグを作成

```bash
$ git push <repository> <tag name>
```

### リモートリポジトリのタグを削除

```bash
$ git push --delete <repository> <tag name>
```

### 登録済みのリモートリポジトリのアドレスを変更

指定した名前で登録されているリモートリポジトリのアドレスを <newurl> のアドレスに変更

```bash
$ git remote set-url <name> <newurl>
```

### 登録済みのリモートリポジトリの名前を変更

```bash
$ git remote rename <old> <new>
```

## サブモジュール(submodule)操作

### サブモジュールの設定
```.gitmodules``` というファイルを作成して以下の内容を記述
```bash
[submodule "<submodule name>"]
	path = <directory name>
	url = <url>
```

e.g.)
```bash
[submodule "themes/hugo-book"]
	path = themes/hugo-book
	url = https://github.com/alex-shpak/hugo-book
```

### サブモジュールを含めて既存のリモートリポジトリを複製
```bash
$ git clone --recursive <url>
```

### サブモジュールを後から複製
```bash
git submodule update --init --recursive
```

## タグ操作

### ローカルのタグの一覧を表示

タグ名のみ
```bash
$ git tag
```

タグ名と注釈
* ```-n``` 表示する注釈の行数
```bash
$ git tag -n1
```

### リモートのタグの一覧を表示

リモート・リポジトリ ```origin``` のタグの一覧を取得
```bash
$ git ls-remote --tags origin
```

### リモートのタグを取得 ```fetch```

```bash
$ git fetch --all --tags
```

### タグ付け (タグを作成)

#### 現在のコミットにタグ付け

注釈なし
```bash
$ git tag <tag name>
```

注釈付き
```bash
$ git tag -m "<tag message>" <tag name>
$ git tag --message "<tag message>" <tag name>
```

注釈付き (viが起動)
```bash
$ git tag -a <tag name>
$ git tag --annotate <tag name>
```

#### 後からコミットにタグ付け

指定したコミット, 注釈付き
```bash
$ git tag -m "<tag message>" <tag name> <commit>
$ git tag --message "<tag message>" <tag name> <commit>
```

指定したコミット, 注釈付き (viが起動)
```bash
$ git tag -a <tag name> -m <tag message> <commit>
$ git tag --annotate <tag name> --message <tag message> <commit>
```

### タグを削除

まだプッシュしていないタグは削除可
```bash
$ git tag -d <tag name>
$ git tag --delete <tag name>
```

### タグを共有

リモート・リポジトリ ```origin``` にプッシュ
```bash
$ git push origin <tag name>
```

すべてのタグをリモート・リポジトリ ```origin``` にプッシュ
```bash
$ git push origin --tags
```

### タグをチェックアウト

```bash
$ git checkout <tag name>
```

タグをチェックアウトすると同時にブランチを作成
```bash
$ git checkout <tag name> -b <branch name>
```

## その他
### 特定のディレクトリやファイルのみチェックアウト
```bash
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

## References
* https://qiita.com/gold-kou/items/7f6a3b46e2781b0dd4a0
* https://qiita.com/jesus_isao/items/63557eba36819faa4ad9
* https://backlog.com/ja/git-tutorial/reference/
* https://qiita.com/harukasan/items/9149542584385e8dea75
* https://qiita.com/kawasaki_dev/items/41afaafe477b877b5b73
