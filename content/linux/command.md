---
date: 2020-01-01
lastmod: ["lastmod"]
title: Linux Command
linkTitle: Command
description: Linux Command
tags: ["Linux","Command"]
---

# Linux Command

## 便利なコマンド

### 文字列の置換

ディレクトリ配下を再帰的に走査しファイルの文字列を置換  
e.g. 拡張子が `.txt` のファイル内の文字列 Before を After に置換
```shell
LC_ALL=C find . -type f -name '*.txt' -exec sed -i '' s/Before/After/g {} +
```

## コマンド入力履歴

### `history` コマンドの主なオプション

|option|description|
|---|---|
|`整数値`|履歴の末尾から指定した行数分を表示|
|`-c`|履歴一覧から全ての項目を削除|
|`-d 番号`|指定した番号の履歴項目を削除|
|`-a`|履歴ファイルに現在のセッションの履歴を追加|
|`-n`|履歴ファイルからまだ読み込まれていない行を全て読み込み|
|`-r`|履歴ファイルを読み込み、内容を履歴一覧に追加|
|`-w`|現在の履歴を履歴ファイルに上書き|

### コマンド入力履歴を表示

履歴を末尾から10件分表示
```shell
$ history 10
```
コマンドライン上で `!番号` と入力することで指定したコマンドを実行  
e.g. 履歴の末尾から5件目のコマンドを実行する場合
```shell
!5
```
履歴の件数が多い場合 `head` コマンドで冒頭のみ表示
```shell
$ history | head
```

### コマンド入力履歴を削除
指定した番号の履歴を削除  
e.g. 5番の履歴を削除
```shell
$ history -d 5
```
全ての履歴を削除
```shell
$ history -c
```

### 現在のコマンド入力履歴を履歴ファイルに上書き
e.g. 現在の履歴を `sample.txt` に上書き
```shell
$ history -w sample.txt
```

### 履歴ファイルを読み込み
e.g. `sample.txt` の内容を履歴として読み込み
```shell
$ history -r sample.txt
```

## 画面操作

### ターミナル操作のショートカット

|Mac|Windows|description|
|---|---|---|
|`control` + `a`|`ctrl` + `a`|カーソルを行頭へ移動|
|`control` + `e`|`ctrl` + `e`|カーソルを行末へ移動|
|`control` + `f`|`ctrl` + `f`|カーソルを前進(右へ移動)|
|`control` + `b`|`ctrl` + `b`|カーソルを後退(左へ移動)|
|`control` + `d`|`ctrl` + `d`|カーソル上の1文字削除|
|`control` + `h`|`ctrl` + `h`|カーソル後方(左)の1文字削除|
|`control` + `u`|`ctrl` + `u`|カーソルから行頭まで削除|
|`control` + `k`|`ctrl` + `k`|カーソルから行末まで削除|
|`control` + `w`|`ctrl` + `w`|カーソル上の単語1つ分削除|
|`control` + `y`|`ctrl` + `y`|最後に削除した文字を貼り付け|
|`control` + `p`|`ctrl` + `p`|1つ前のコマンド履歴を表示|
|`control` + `n`|`ctrl` + `n`|1つ後のコマンド履歴を表示|
|`control` + `s`|`ctrl` + `s`|画面をロック|
|`control` + `q`|`ctrl` + `q`|画面のロックを解除|
|`control` + `c`|`ctrl` + `c`|実行中のコマンドを強制終了|
|`control` + `l(エル)`|`ctrl` + `l(エル)`|画面をクリア|
|`tab`|`tab`|コマンド,ディレクトリ,ファイルの候補を補完|


## 文字コード

### 文字コードの値

|文字コード|コマンドで指定する値|remarks|
|---|---|---|
|Shift_JIS|`cp932` or `sjis`|`cp932` には `sjis` に含まれていない文字（①, ②, Ⅰ, Ⅱ, ㍉, ㌍, 彅, 髙 etc.）を含む。|
|EUC-JP|`euc-jp`|-|
|UTF-8|`utf-8`|-|

### 文字コードを設定

`.vimrc`
以下を記述し `:wq` で保存。
```shell
:set encoding=utf-8
:set fileencodings=utf-8,cp932,sjis,euc-jp
```

|long|short|description|
|---|---|---|
|`encoding`|`enc`|`vim``` の内部で使用されるエンコーディングを指定。|
|`fileencoding`|`fenc`|編集時のバッファファイルのエンコーディングを指定。encodingと異なる値が設定されていた場合、ファイルの読み書き時に文字コードを変換。fenc が空の場合、変換なし。|
|`fileencodings`|`fencs`|既存ファイル編集時の変換予定の文字コードを指定。カンマ区切りで複数記述可。

### vim 内部で使用される文字コードを確認
```shell
:set encoding?
```
or
```shell
:se enc?
```

### ファイルの文字コードを確認
```shell
:set fileencoding?
```
or
```shell
:se fenc?
```

### ファイルの文字コードの自動判別設定を確認
```shell
:set fileencodings?
```
or
```shell
:se fencs?
```

### 指定の文字コードでファイルを開き直し
```shell
:edit ++encoding=コマンドで指定する文字コードの値
```
or
```shell
:e ++enc=コマンドで指定する文字コードの値
```

### ファイルの文字コードを変換
以下を実行後に `:w` で保存すること。
```shell
:set fileencoding=コマンドで指定する文字コードの値
```
or
```shell
:set fenc=コマンドで指定する文字コードの値
```

### ファイルの文字コードを変換し保存
```shell
:setlocal fileencoding=コマンドで指定する文字コードの値
```
or
```shell
:setl fenc=コマンドで指定する文字コードの値
```

## 改行コード

### 改行コードの設定

`.vimrc`
以下を記述し `:wq` で保存。
```shell
:set fileformats=unix,dos,mac
```

### 改行コードの値

|改行コード|コマンドで指定する値|remarks|
|---|---|---|
|LF(Line Feed) ```\n`|`unix`|Unix, Linux, Mac(OS10以降), Android|
|CR(Carriage Return) ```\r`|`mac`|古いMac(OS9以前)|
|CRLF(Carriage Return/Line Feed) ```\r\n`|`dos`|Windows|

### 改行コードを指定
以下を実行後に `:w` で保存すること。
```shell
:set fileformat=コマンドで指定する改行コードの値
```
or
```shell
:se ff=コマンドで指定する改行コードの値
```

### 改行コードを指定し保存

```shell
:setlocal fileformat=コマンドで指定する改行コードの値
```
or
```shell
:setl ff=コマンドで指定する改行コードの値
```

## ディスク容量

### `df` コマンドとは
ディスクの空き領域(free スペース)のサイズを集計して表示

### `df` コマンドの主なオプション

|option|option|description|
|---|---|---|
|`-h`|`--human-readable`|サイズに応じて人間が読みやすい単位に変換して表示|
|`-H`|`--si`|イズに応じて人間が読みやすい単位に変換して表示、ただし 1024単位ではなく 1000単位の値を使用|
|`-Bサイズ`|`--block-size=サイズ`|指定したサイズの倍数で表示、サイズは数値または単位で指定|
|`-k`||`--block-size=1K``` と同じ|
|`-m`||`--block-size=1M``` と同じ|
|`-P`|`--portability`|POSIX出力形式（サイズではなくブロックで表示）|
|`-i`|`--inodes`|ブロック使用量の代わりにiノード情報を表示|
|`-T`|`--print-type`|各ファイルシステムの種類を合わせて表示|
|`-t種類`|`--type=種類`|対象とするフォーマットの種類を指定(e.g.「-txfs」でXFSフォーマットの場所のみ表示)|
|`-x種類`|`--exclude-type=種類`|対象としないフォーマットの種類を指定|
|`-a`|`--all`|ダミーファイルシステムを含めて表示|
|`-l`|`--local`|ローカルファイルシステムのみを表示|
||`--total`|全体の合計も表示|
||`--sync`|使用量の情報を得る前にsyncを実行|
||`--no-sync`|使用量の情報を得る前にsyncの実行なし（デフォルト）|

### ディスクの空き容量を表示
```shell
df
```
メガ(1024×1024バイト)単位で表示
```shell
df -BM
```

メガ(1000×1000バイト)単位で表示
```shell
df -BMB
```
フォーマットの種類も合わせて表示
```shell
df -T
```
XFSでフォーマットされているパーティションの空き領域と合計を表示
```shell
df -txfs --total
```
### ディスクの空き容量を表示(場所を指定して集計)
カレントディレクトリを含むパーティションの空き領域を表示
```shell
df .
```
`/usr` を含むパーティションの空き領域を表示
```shell
df /usr
```

### ディスクの空き容量を読みやすいサイズ表記で表示
```shell
df -h
```
空き領域の合計もあわせて表示
```shell
df -h --total
```

## 日付・時刻

```shell
TOMORROW=`date '+%Y%m%d' --date '1 day'`
YESTERDAY=`date '+%Y%m%d' --date '-1 day'`
echo ${TOMORROW}
echo ${YESTERDAY}
```
### `date` コマンドの主なオプション

|format|description|
|---|---|
|`%H`|時 (00～23)|
|`%I`|時 (01～12)|
|`%k`|時 ( 0～23)|
|`%l`|時 ( 1～12)|
|`%M`|分 (00～59)|
|`%p`|AM あるいは PM のロケール(国や地域に合わせた文字列)|
|`%r`|12時間形式の時刻 (HH:mm:ss [AP]M)|
|`%s`|1970-01-01 00:00:00 UTC からの秒数|
|`%S`|秒 (00～61)|
|`%T`|24時間形式の時刻 (HH:mm:ss)|
|`%a`|ロケールによる省略形の曜日の名前 (Sun～Sat)|
|`%A`|ロケールによる完全に表記した曜日の名前(Sunday～Saturday)|
|`%b`|ロケールによる省略形の月の名前 (Jan～Dec)|
|`%B`|ロケールによる完全に表記した月の名前(January～December)|
|`%c`|ロケールによる日付と時刻 (Sat Nov 04 12:02:33 EST 1989)|
|`%d`|日(月内通算日数) (01～31)|
|`%D`|日付 (MM/DD/YY)|
|`%j`|年内通算日数 (001～366)|
|`%m`|月 (01～12)|
|`%w`|週のうちの曜日(0～6)で0が日曜日に対応|
|`%x`|ロケールによる日付の表現 (MM/DD/YY)|
|`%y`|西暦の下2けた (00～99)|
|`%Y`|年 (1970～)|

### タイムゾーンを確認

```shell
$ date +%Z
JST
```
```shell
$ date +"%Z %z"
JST +0900
```

## 検索

### `grep` コマンドの使用方法
```shell
grep 検索したい文字列 検索したいテキストファイル
```
任意のコマンドの実行結果に対して検索
```shell
コマンド | grep 検索したい文字列
```
複数のパターンを指定
```shell
grep -e 検索したい文字列1 -e 検索したい文字列2 検索したいテキストファイル
```
指定したディレクトリ配下を再帰的に検索, 検索結果に行番号を表示
```shell
grep -rn -e 'pattern' /dir/
```
### `grep` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-e 'パターン'`|`--regexp='パターン'`|パターンを指定して検索|
|`-f`|`--file=ファイル`|ファイルに記載したパターン(1行1パターン)を読み込み検索に使用|
|`-i`|`--ignore-case`|アルファベットの大文字と小文字を区別せず検索|
|`-w`|`--word-regexp`|単語全体でパターンと一致するものを検索|
|`-x`|`--line-regexp`|行全体がパターンと一致するものを検索|
|`-G`|`--basic-regexp`|基本正規表現(BRE)を検索に使用
|`-E`|`--extended-regexp`|拡張正規表現(ERE)を検索に使用|
|`-P`|`--perl-regexp`|Perl互換正規表現(PCRE)を検索に使用|
|`-v`|`--invert-match`|パターンに一致しない行を表示|
|`-s`|`--no-messages`|エラーメッセージを非表示|
|`-q`|`--quiet` `--silent`|結果を非表示(主にシェルスクリプトなどで判定用に使用|
|`-L`|`--files-without-match`|一致するものが含まれていないファイルのファイル名のみ表示|
|`-l`|`--files-with-matches`|一致するものが含まれているファイルのファイル名のみ表示|
|`-c`|`--count`|一致するものが含まれている回数のみ表示|
|`-m 回数`|`--max-count=回数`|パターンが指定した回数に達したら処理を終了|
|`-o`|`--only-matching`|一致した箇所だけを表示(通常は行単位で表示)|
|`-n`|`--line-number`|行番号を併せて表示(「行番号付きで表示」を参照)|
|`-b`|`--byte-offset`|パターンが何文字目にあるかを併せて表示|
|`-H`|`--with-filename`|ファイル名を併せて表示|
|`-h`|`--no-filename`|ファイル名を表示しない(複数ファイル指定時)|
||`--color=WHEN`|見つかったパターンを目立たせる(WHEN部分にはalways、never、autoを指定)|
|`-B 行数`|`--before-context=行数`|一致した行の前の行も表示(「前後の行も表示」を参照)|
|`-A 行数`|`--after-context=行数`|一致した行の後の行も表示(「前後の行も表示」を参照)|
|`-C 行数`,`-行数`|`--context=行数`|一致した行の前後の行も表示|
|`-r`|`--recursive`|ディレクトリを指定した場合はサブディレクトリ内のファイルも含め検索|

### タイムスタンプでの検索
#### 現在〜2日前(48時間前)
```shell
find ./ -mtime -2
```

#### 2日前(48時間前〜72時間前)
```shell
find ./ -mtime 2
```

#### 2日前(72時間前)〜過去
```shell
find ./ -mtime +1
```

e.g) 特定のディレクトリ内の30日以前のCSV拡張子のファイルを削除
```shell
find ./ -type f -name '*.csv' -mtime +30 | xargs rm -f
```

### `find` コマンドの主なオプション
|option|description|
|---|---|
|`-mmin`|ファイルのデータの最終更新日時（分指定）|
|`-mtime`|ファイルのデータの最終更新日時（日指定）|
|`-amin`|ファイルのデータの最終アクセス日時（分指定）|
|`-atime`|ファイルのデータの最終アクセス日時（日指定）|
|`-cmin`|ファイルのデータとステータスの最終更新日時（分指定）|
|`-ctime`|ファイルのデータとステータスの最終更新日時（日指定）|
|`-daystart`|00:00am 基準|


## 一覧表示

### タイムスタンプ降順で一覧表示(隠しファイル含む)
```shell
ls -alt
```
### タイムスタンプ昇順で一覧表示(隠しファイル含む)
```shell
ls -altr
```

### `ls` コマンドの主なオプション

|option|description|
|---|---|
|`-a`|先頭 `.` の隠しファイルも表示|
|`-A`|先頭 `.` の隠しファイル, 現在のディレクトリ `.`, 親ディレクトリ `..` も表示|
|`-l(エル)`|詳細情報を表示|
|`-t`|タイムタンプ降順で表示|
|`-tr`|タイムタンプ昇順で表示|

## 移動・名称変更

※ 基本的にファイルもディレクトリも操作は同じ

### 1つのファイルを移動
e.g) ファイル `sample.txt` を ディレクトリ `./dir/` に移動
```shell
mv sample.txt ./dir/
```
### 複数のファイルを移動
e.g) ファイル `sample1.txt` と `sample2.txt` を ディレクトリ `./dir/` に移動
```shell
mv sample1.txt sample2.txt ./dir/
```
### ファイルの名称を変更(リネーム)
ファイル `before.txt` を `after.txt` に変更
```shell
mv before.txt after.txt
```

### `mv` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-v`|`--verbose`|実行内容を表示|
|`-i`|`--interactive`|移動元と移動先が同名の場合, 上書きする前に確認|
|`-b`|`--backup=方法`|移動元と移動先が同名の場合, 上書きされるファイル(移動先)のバックアップを作成｜
|`-S`|`--suffix=接尾語`|バックアップ作成時の接尾語を指定|
|`-n`|`--noclobber`|移動元と移動先が同名の場合, 上書きせず停止|
|`-u`|`--update`|移動元のファイルが移動先ファイルより新しいか新規ファイルの場合のみ上書き|

## 削除

### 1ファイルを削除
```shell
$ rm sample.txt
```

### 複数ファイルを削除
```shell
$ rm sample-1.txt sample-2.txt sample-3.txt
```

### カレントディレクトリの全ファイルを確認しながら削除
```shell
$ rm -i *
```

### `rm` コマンドの主なオプション

|option|option|description|
|---|---|---|
|`-f`|`--force`|削除前の確認なし(指定したファイルが存在しない場合もエラー表示なし)|
|`-i`|`--interactive`|削除前に確認|
|`-v`|`--verbose`|経過を表示|
|`-d`|`--directory`|`unlink` でディレクトリを削除|
|`-r`,`-R`|`--recursive`|ディレクトリを再帰的に削除(ファイルを含む)|
||`--preserve-root`|`/` の削除なし (`/` を特別なものとして扱う, デフォルト)|
||`--no-preserve-root`|`/` の削除あり (`/` を特別なものとして扱わない)|

## 空ディレクトリを削除

```shell
$ rmdir dir
```

多階層の空ディレクトリを削除
```shell
$ rmdir -p dir1/dir2/dir3
```

### `rmdir` コマンドの主なオプション

|option|option|description|
|---|---|---|
||`--ignore-fail-on-non-empty`|空ディレクトリでない場合のエラーのみ無視(エラーメッセージの表示なし)|
|`-p`|`--parents`|必要に応じて親ディレクトリも削除|
|`-v`|`--verbose`|経過を表示|

## コピー

※ 基本的にファイルもディレクトリも操作は同じ

### ファイルをコピー
```shell
cp -ip original.txt copy.txt
```
### ディレクトリごとファイルをコピー
```shell
cp -ipr original-dir copy-dir
```
### ファイルをコピーする際にバックアップを作成
```shell
cp -b --suffix=_$(date +%Y%m%d%H%M%S) original.txt copy.txt
```

### `cp` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-v`|`--verbose`|実行内容を表示|
|`-i`|`--interactive`|コピー元とコピー先が同名の場合, 上書きする前に確認|
|`-b`|`--backup=方法`|コピー元とコピー先が同名の場合, 上書きされるファイル(コピー先)のバックアップを作成｜
|`-S`|`--suffix=接尾語`|バックアップ作成時の接尾語を指定|
|`-u`|`--update`|コピー元のファイルがコピー先ファイルより新しいか新規ファイルの場合だけコピー|
|`-r`|`--recursive`|コピー元にディレクトリを指定した場合 再帰的にコピー|
|`-p`|`--preserve`|コピー元のパーミッションと所有者とタイムスタンプを保持（--preserve=mode,ownership,timestampsと同じ）|
|`-s`|`--symbolic-link`|シンボリック・リンクを作成|


### ネットワーク上のホスト間のSSHを利用したコピー

```shell
scp [option] [転送元ユーザー名@][転送元ホスト名:]転送元ファイルパス 転送先ユーザ名@転送先ホスト名:転送先パス
```
### `scp` コマンドの主なオプション
|option|description|
|---|---|
|`-r`|転送元のディレクトリ全体を再帰的にコピー|
|`-v`|転送の進行情報を表すメーターやデバッグ・メッセージを表示|
|`-q`|転送の進行情報を表すメーターを非表示|
|`-C`|通信を圧縮|
|`-p`|転送元の最終更新日時・最終アクセス日時・パーミッションを転送先に保持|
|`-P ポート番号`|転送先のポート番号を指定|
|`-i 秘密鍵ファイル`|秘密鍵ファイルを指定|


## ディレクトリとファイルの構成を表示

### tree コマンド のインストール (macOS)
```shell
$ brew install tree
```

### tree コマンドのバージョンを確認
```shell
$ tree --version
```

### 階層を指定
e.g. カレントディレクトリ配下の3階層まで表示する場合
```shell
$ tree -L 3
```

### ファイル名のパターンを指定
```shell
$ tree -P '*.js'
```

### 除外するファイル名のパターンを指定
```shell
$ tree -I '*.log'
```

### ディレクトリのみを表示
```shell
$ tree -d
```

### `tree` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-a`||ドット始まりのディレクトリを含めて表示|
|`-d`||ディレクトリのみ表示|
|`-l`||シンボリックをたどって表示|
|`-f`||カレントディレクトリまたは指定したディレクトリからのパスをつけてファイルやディレクトリを表示|
|`-L 整数`||表示するディレクトリの階層(深さ), 1以上の整数を指定|
|`-N`||印字できない文字をそのまま出力(マルチバイトの文字化け防止)|
|`-P パターン`||パターンにマッチしたファイルのみ表示|
|`-l パターン`||パターンにマッチしないファイルのみ表示|
||`--prune`|表示対象ファイルが存在しないディレクトリを除外して表示|
||`--filelimit エントリ数`|指定した数を超えるエントリを持つディレクトリの内部は非表示(--filelimit 10 の場合, 表示内容が10イないのディレクトリの内部のみを表示)|
|`-o ファイル名`||`tree`コマンドの結果を指定ファイルに出力|

## `ln` ディレクトリやファイルのリンクを作成

### ハードリンクとシンボリックリンク
* シンボリックリンクでファイルの「別名」なら、ハードリンクはファイルの「本名」。
* ハードリンクの場合、元ファイルもリンクもどちらも「本名」。両者は同等。
### ディレクトリのシンボリックリンクを作成
```shell
ln -s ディレクトリ名 リンク名
```

### ファイルのシンボリックリンクを作成
```shell
ln -s ファイル名 リンク名
```

### 指定したディレクトリ内にシンボリックリンクを作成
```shell
ln -s ファイル1 ファイル2・・・ ディレクトリ名
```

### ディレクトリのハードリンクを作成
```shell
ln ディレクトリ名 リンク名
```

### ファイルのハードリンクを作成
```shell
ln ファイル名 リンク名
```

### `ln` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-s`|`--symbolic`|ハードリンクの代わりにシンボリックリンクを作成|
|`-d`,`-F`|`--directory`|ディレクトリのハードリンクを作成(スーパーユーザのみ)|
|`-f`|`--force`|リンクファイルと同名のファイルがある場合も強制的に上書き|
|`-i`|`--interactive`|上書きする場合に確認|
|`-n`|`--no-dereference`|リンク作成場所として指定したディレクトリがシンボリックリンクだった場合、参照先にリンクを作成するのではなく、シンボリックリンクそのものを置き換え(-fと組み合わせて使用)|
|`-L`|`--logical`|対象がシンボリックリンクの場合リンクを巡る|
|`-P`|`--physical`|シンボリック自体のハードリンクを作成|
|`-r`|`--relative`|相対パスのシンボリックリンクを作成|
|`-T`|`--no-target-directory`|リンク先を常に通常ファイルとして扱う|
|`-t ディレクトリ名`|`--target-directory=ディレクトリ名`|指定したディレクトリにリンクを作成|
|`-b`|`--backup=方法`|上書きされるバックアップを作成。`numbered` または `t` の場合は番号の添字。`simple` または `never` の場合はファイルの末尾に `~` または `-S` オプションか環境変数で指定した文字をつけるシンプルなバックアップ(`-b` と同じ)。`existing` または `nil` の場合はすでにあるファイルに従う(ない場合は `simple` と同じ)|
|`-S 接尾辞`|`--suffix=接尾辞`|バックアップファイル作成時にファイル末尾に付与する文字を指定(未指定の場合は ~ または環境変数 `SIMPLE_BACKUP_SUFFIX`)|
|`-v`|`--verbose`|経過を表示|

## `mkdir` ディレクトリの作成

### 多階層のディレクトリを作成

```shell
mkdir -p /dir/subdir/{subsubdir-1,subsubdir-2,subsubdir-3}
```
```shell
dir
└── subdir
    ├── subsubdir-1
    ├── subsubdir-2
    └── subsubdir-3
```

### 作成したディレクトリに作業場所を移動
```shell
mkdir -p /dir/subdir/subsubdir/ && cd $_
```

## ファイルの作成

### 空ファイルを作成
```shell
touch sample.txt
```

### ファイルのタイムスタンプを変更
e.g) ファイルのタイムスタンプ(最終更新日)を「2021-01-01 01:25:30」に変更
```shell
touch --date="2021-01-01 01:25:30" sample.txt
```
or
```shell
touch -t 202101012530.30 sample.txt
```

|short option|long option|description|
|---|---|---|
|`-t <time>`|-|[[CC]YY]MMDDhhmm[.ss] 形式で日時を指定|
|`-d`|-date=日付文字列|文字列で日時を指定|
|`-r`|--reference=ファイル名|指定したファイルと同じ日時を指定|


## `cat` ファイル内容の表示

### `cat` コマンドとは
ファイルを連結するためのコマンドだが、ファイルの内容を簡単に表示する際に使用されている。
```cat``` = con```CAT```enate (つなぐ, 連結する)

### ファイルの内容を行番号付きで表示
```shell
$ cat -n sample.txt
```

### 複数のファイルを連結して 1つのファイルに出力
```shell
$ cat before1.txt before2.txt > after.txt
```


### `cat` コマンドの主なオプション
|option|option|description|
|---|---|---|
|-n|--number|行番号を付与|
|-b|--number-nonblank|行番号を付与(空白行以外)|
|-s|-squeeze-blank|連続した空行を1行として表示|
|-v|--show-nonprinting|タブ, 改行, 改ページ以外の非表示文字を表示|
|-t||非表示文字を表示(タブを `^\|`, 用紙送りを `^L` として表示|
|-E|--show-ends|行の最後に `$` を表示|
|-A|--show-all|全ての非表示文字を表示(`-vET` と同じ)|
|-e||タブを除く全ての非表示文字を表示(`-vE` と同じ)|

## `less` ファイル内容の表示

### ファイル内容を1画面に表示(編集なし)

```shell
less sample.txt
```
行番号も表示
```shell
less -N sample.txt
```
### ファイル内容表示時のショートカット
`less` コマンド
|command|description|
|---|---|
|`d`|半画面前進(下へ移動)|
|`u`|半画面後退(上へ移動)|
|`g`|先頭行へ移動|
|`G`|最終行へ移動|
|`v`|`vi` コマンドで編集|
|`q`|終了|
|`/`|前方(下方向)へ検索 (n 下方向, N 上方向)|
|`?`|後方(上方向)へ検索 (n 下方向, N 上方向)|

## ファイル内容の編集
ファイルの作成と編集
`vi sample.txt`

### 入力モードへの切り替え
|command|description|
|---|---|
|`i`|現在のカーソル位置に入力|
|`A`|現在のカーソル位置の行末に入力|
|`O`|1つ上の行に文字を入力|
|`o`|1つ下の行に文字を入力|

### 入力モードからコマンドモードへの切り替え
|command|description|
|---|---|
|`esc`|入力モードからコマンドモードへ切り替え|

### コマンドモードの操作
|command|description|
|---|---|
|`x`|1文字削除|
|`dd`|1行削除|
|`d100d`|100行削除|
|`y`|行コピー|
|`y100y`|100行コピー|
|`p`|コピーした行の貼り付け|
|`G`|最終行へ移動|
|`100G`|100行目へ移動|
|`/`|前方(下方向)へ検索|
|`?`|後方(上方向)へ検索|
|`:q!`|ファイルを保存せずに終了(閉じる)|
|`:wq`|ファイルを保存して終了(閉じる)|
|`:w`|ファイルを保存のみ(viは終了しない状態)|

## ファイル内容の行数・並べ替え・重複

### ファイルの行数
#### ファイルの行数を出力
```shell
wc -l sample.txt
```

#### ファイルの単語数を出力
```shell
wc -w sample.txt
```

#### ファイルのバイト数を出力
```shell
wc -c sample.txt
```

#### ファイルの空行の行数を出力
```shell
grep -c '^$' sample.txt
```

#### 指定した文字と一致しない行数を出力
```shell
grep -c -v 'hoge' sample.txt
```

### ファイル内容の並べ替え
#### 並べ替えた結果を出力
```shell
sort sample.txt
```
#### 並べ替え重複行は1行目のみを出力
```shell
sort -u sample.txt
```
```shell
sort --unique sample.txt
```

#### 並べられているか確認
```shell
sort -c
```
```shell
sort --check
```

### ファイル内容の重複

#### 重複行は1行目のみを出力
```shell
uniq sample.txt
```

#### 重複する行のみ出力
```shell
uniq -d sample.txt
```

### ファイル内容の正規表現での確認
#### 正規表現と一致する行数を確認
e.g.) 半角数字10桁のみの行数
```shell
grep -c -E '^[0-9]{10}$' sample.txt
```

## 圧縮・解凍・暗号解除

### `zip` コマンドでの圧縮

`zip`圧縮後に元ファイルを削除
```shell
zip -rm after.zip before.file
```

パスワード付き `zip` ファイルの作成
```shell
zip -e -r after.zip before/
```

### `zip` コマンドでの展開
```shell
$ unzip sample.zip
```

### `zip` ファイルの暗号解除

```shell
$ zipcloak -d sample.zip
```

`encrypted_data.zip` の暗号を解除し `decrypted_data.zip` に保存
```shell
$ zipcloak -d -O decrypted_data.zip encrypted_data.zip
```

### `zipcloak` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-d`|`--decrypt`|暗号を解除 (間違ったパスワードを入力した場合、```zip``` ファイルを元のまま上書き。|
|`-O ZIPファイル名`|`--output-file ZIPファイル名`|暗号を解除したZIPファイルの保存先を指定。 (元のZIPファイルはそのまま残る。)|
|`-b``` ```パス|--temp-path`|一時作業用のディレクトリを指定。|
|`-q`|`--quiet`|動作中のメッセージを非表示。|

### `tar`コマンドでの圧縮
`tar` コマンドでファイルのアーカイブを作成し `gzip` 形式に圧縮

```shell
$ tar -czvf after.tar.gz before.txt
```
カレントディレクトリの全ファイルをまとめたアーカイブを作成し `gzip` 形式に圧縮
```shell
$ tar -czvf after.tar.gz *
```
### `tar` コマンドでの展開(解凍)
`gzip` 形式で圧縮されたアーカイブを `tar` コマンドで展開

```shell
$ tar -xzvf sample.tar.gz
```

### `tar` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-c`|`--create`|新しいアーカイブを作成|
|`-r`|`--append`|アーカイブの最後にファイルを追加|
|`-A`|`--catenate`, `--concatenate`|アーカイブに tarアーカイブを追加|
|`-u`|`--update`|アーカイブのファイルを更新 (アーカイブ内の同名ファイルより新しいものだけを追加)|
|`-d`|`--diff`, `--compare`|アーカイブとファイルシステムを比較|
||`--delete`|アーカイブから削除|
|`-t`|`--list`|アーカイブの内容の一覧を表示|
|`-x`|`--extract`, `--get`|アーカイブからファイルを抽出|

### `tar` コマンドの共通のオプション
|option|option|description|
|---|---|---|
|`-z`|`--gzip`|アーカイブを `gzip` 形式で圧縮。`gzip` 形式で圧縮されたアーカイブを展開。拡張子は `.tar.gz` または `.tgz`。|
|`-j`|`--bzip2`|アーカイブを `bzip2` 形式で圧縮。`bzip2` 形式で圧縮されたアーカイブを展開。拡張子は `.tar.bz2`。|
|`-Z`|`--compress`, `--uncompress`|アーカイブを `compress` 形式で圧縮。`compress` 形式で圧縮されたアーカイブを展開。拡張子は `.tar.Z`。|
|`-W`|`--verify`|アーカイブを書き出した後に検証。|
|`-v`|`--verbose`|処理したファイルを詳しく出力。|

## CSV
### CSVファイルの各行のカラム数が指定した数(以下の例では 10)と一致しない行数を確認
```shell
cat sample.csv | awk -F ',' '{print NF}' | grep -c -v 10
```

### CSVファイルの特定のカラム(以下の例では10カラム目)のみを抽出
```shell
cut -d ',' -f 10 sample.csv > specific_column.csv
```

## システム情報/バージョン

### システム情報を確認
```shell
uname -a
```

### カーネルのバージョン情報を確認
```shell
cat /proc/version
```

### OSのバージョン情報を確認
```shell
cat /etc/os-release
```

### OSのバージョン情報を確認(ディストリビューション別)
※RHEL=Red Hat Enterprise Linux
|command                      |RHEL※|CentOS|Fedora|Amazon Linux|Oracle Linux|Debian|Ubuntu   |SUSE Linux|
|-----------------------------|-----|------|------|------------|------------|------|---------|----------|
|`cat /etc/system-release`|Yes  |Yes   |Yes   |Yes         |Yes         |      |         |          |
|`cat /etc/redhat-release`|Yes  |Yes   |Yes   |            |Yes         |      |         |          |
|`cat /etc/centos-release`|     |Yes   |      |            |            |      |         |          |
|`cat /etc/fedora-release`|     |      |Yes   |            |            |      |         |          |
|`cat /etc/oracle-release`|     |      |      |            |Yes         |      |         |          |
|`cat /etc/debian_version`|     |      |      |            |            |Yes   |Yes      |          |
|`cat /etc/lsb-release`   |     |      |      |            |            |      |Yes      |          |
|`cat /etc/SuSE-release`  |     |      |      |            |            |      |         |Yes       |


### OSのバージョン情報確認コマンドを確認
```shell
ls -l /etc/*release
```

## ディスク使用量を確認

### `du` コマンドとは
* ディスク使用量を集計し表示
* アクセス権限のないファイルやディレクトリは集計対象外

### `du` コマンドの主なオプション
#### 集計
|option|option|description|
|---|---|---|
|-S|--separate-dirs|サブディレクトリのサイズを含めずに集計|
|-s|--summarize|指定したディレクトリの合計のみ表示(サブディレクトリの行は非表示)|
|-d深さ|--max-depth=深さ|集計するディレクトリの深さを指定|
|-P|--no-dereference|シンボリックをたどらずに集計(デフォルト)|
|-L|--dereference|全てのシンボリックをたどって集計|
|-D,-H|--dereference-args|コマンドラインで指定されたシンボリックのみたどって集計|
|-x|--one-file-system|異なるファイルシステム(パーティーション)にあるディレクトリをスキップして集計|
|-l|--count-links|ハードリンクがある場合、その個数分のサイズを数える|
||--exclude=パターン|パターンと一致するファイルを除外して集計|
|-Xファイル|--exclude-from=ファイル|ファイルに含まれるパターンに一致するファイルを除外して集計|
||--files0-from=ファイル|ファイルに指定されたリスト(NULL区切り)を対象に集計、`-` を指定した場合は標準入力から読み込み|

#### 表示
|option|option|description|
|---|---|---|
|-h|--human-readable|サイズに応じて人間が読みやすい単位に変換して表示|
|-H|--si|イズに応じて人間が読みやすい単位に変換して表示、ただし 1024単位ではなく 1000単位の値を使用|
|-Bサイズ|--block-size=サイズ|指定したサイズの倍数で表示、サイズは数値または単位で指定|
|-k||--block-size=1K と同じ|
|-m||--block-size=1M と同じ|
||--apparent-size|ディスク使用量ではなく実際のサイズを表示|
|-b|--bytes|実際のサイズをバイト単位で表示（ `--apparent-size` または `--block-size=1` に相当）|
|-c|--total|全体の合計も表示|
|-0|--null|改行ではなく NULL で区切って表示|
||--time|集計対象となったファイルの最終更新時間を対象ごとに表示|
||--time=表示|表示の値に指定した `atime` `access` `use` `ctime` `status` の時間を表示|
||--time-style=スタイル|時間の表示スタイルとして `full-iso` `long-iso` `iso` `+FORMAT` を指定、`+FORMAT` は `date` コマンドのフォーマット指定と共通|

### 特定ディレクトリ配下でディスク使用量が大きい順に表示
```shell
du -hs /path/to/target/directory/* | sort -hr | head -10
```

### カレントディレクトリ直下のファイルやディレクトリのディスク使用量とその合計を表示
```shell
$ du -cs *
```

### 集計するディレクトリの深さを指定
e.g. ルートディレクトリとその直下のディスク容量を1階層まで表示  
(他のパーティーションにあるファイルは対象外)  
`-x` 1つのファイルシステムのみを集計
```shell
$ du -d1 -x /
```

## 並べ替え

### `sort` コマンドとは
* テキストファイルを行単位で並べ替えるコマンド

### `sort` コマンドの主なオプション

#### 並べ替え関連
|option|option|description|
|---|---|---|
|`-f`|`--ignore-case`|大文字／小文字を区別せずに並べ替え|
|`-V`|`--version-sort`|自然な（バージョン）数字順で並べ替え|
|`-n`|`--numeric-sort`|文字列を数値と見なして並べ替え|
|`-h`|`--human-numeric-sort`|人が読むことのできる形式の数値で並べ替え(e.g. 5K, 1.2G etc.)|
|`-g`|`--general-numeric-sort`|一般的な数値として並べ替え|
|`-M`|`--month-sort`|月名で並べ替え|
|`-d`|`--dictionary-order`|データが空白と英数字のみ含まれていると仮定して並べ替え|
|`-b`|`--ignore-leading-blanks`|先頭の空白を無視して並べ替え|
|`-i`|`--ignore-nonprinting`|表示可能な文字だけを対象に並べ替え|
|`-R`|`--random-sort`|キーのランダムハッシュ順に並べ替え|
||`--random-source=ファイル`|ランダムソースのファイルを設定|
||`--sort=指定`|並べ替えの方法を指定 (general-numeric、human-numeric、month、numeric、random、version)|
|`-r`|`--reverse`|逆順で並べ替え|
|`-k 指定`|`--key=指定`|所と並べ替え種別を指定する (`-k 5`なら5列目、`-k 5n` なら5列目を数値として並べ替え。複数指定する場合は `-k` オプションを複数回指定)|
|`-t 文字`|`--field-separator=文字`|フィールドの区切り文字を指定（デフォルトは空白文字）|

#### その他
|option|option|description|
|---|---|---|
|`-m`|`--merge`|並べ替えられたファイルをマージ (並べ替え自体は行わない)|
|`-c`|`--check`, `--check=diagnose-first`|並べ替えられているかどうかを確認|
|`-C`|`--check=quiet`, `--check=silent`|`-c` と同様だがメッセージは出力しない（スクリプト内の判定行などで使用）|
|`-u`|`--unique`|同一行は1つ目だけを出力（`-c` と併せて使用した場合、厳密に順序を確認）|
|`-z`|`--zero-terminated`|最後に NULL 文字を出力|
|`-o ファイル名`|`--output=ファイル名`|結果を出力するファイル名を指定（デフォルトは標準出力）|
||`--files0-from=F`|NULL文字で区切られたファイル名のリストを指定（`--files0-from=-` とした場合、ファイル名を標準入力から読み込み|
||`--debug`|並べ替えに使用されている行の一部に注釈を付けて、不確かな使用方法について標準エラー出力に警告を表示|

### 指定した列で並べ替え
区切り文字を `,` として 5列目の項目で並べ替え
```shell
sort -k 5 -t , sample.csv
```
区切り文字を `,` として 5列目の項目を数字として並べ替え
```shell
sort -k 5n -t , sample.csv

```

## 環境

### `env` コマンドとは
環境変数を指定してコマンドを実行
```shell
env 環境変数名=値 コマンド
```

e.g.  
環境変数を一覧表示

```shell
$ env
```

環境変数を指定しないで実行
```shell
$ date
Mon Jan 10 10:30:00 JST 2022
```
一時的に特定の環境変数を設定して(上書きして)実行
```shell
$ env LANG=ja_JP.UTF-8 date
2022年 1月10日 月曜日 10時30分10秒 JST
```
一時的に全ての環境変数を未設定の状態で実行
```shell
$ env -i date
```


### `env` コマンドの主なオプション
|option|option|description|
|---|---|---|
|`-i`, `-`|`--ignore-environment`|一時的に「全ての環境変数が未設定の状態」でコマンドを実行|
|`-u 環境変数名`|`--unset=環境変数名`|一時的に「指定した環境変数が未設定の状態」でコマンドを実行|
|`0`|`--null`|改行せずに表示（出力行の末尾を改行文字ではなくNULL文字にする、一覧表示する際のみ指定可能）|


### 指定可能なロケールを確認
```shell
$ locale -a
```
## その他



### ログイン中のユーザー
```shell
LOGIN_USER=`who am i | awk -F ' ' '{print $1}'`
echo $LOGIN_USER
```

## References

* [Linux基本コマンドTips](https://atmarkit.itmedia.co.jp/ait/series/3065/)
* [インフラエンジニアとしてよく使うコマンド集](https://qiita.com/sion_cojp/items/04a2aa76a1021fe77079)
* [インフラエンジニアじゃなくても押さえておきたいSSHの基礎知識](https://qiita.com/tag1216/items/5d06bad7468f731f590e)
* [viの文字コードについて](http://capm-network.com/?tag=vi%E3%81%AE%E6%96%87%E5%AD%97%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)
* [vimによる日本語文字コード自動判別と文字コード変換](https://itcweb.cc.affrc.go.jp/affrit/faq/tips/vim-enc)
* [grepでこういう時はどうする?](https://qiita.com/hirohiro77/items/771ffb64dddceabf69a3)
* [Man page of GREP](https://linuxjm.osdn.jp/html/GNU_grep/man1/grep.1.html)
* [gzip ファイルを操作](https://www.atmarkit.co.jp/ait/articles/1810/18/news014.html)
* [【 env 】コマンド――環境変数を指定してコマンドを実行する](https://atmarkit.itmedia.co.jp/ait/articles/1801/18/news016.html)
* [【 tree 】コマンド――ディレクトリをツリー状に表示する](https://atmarkit.itmedia.co.jp/ait/articles/1802/01/news025.html)
