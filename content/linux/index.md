---
date: 2020-01-01
lastmod: ["lastmod"]
title: Linux
tags: ["Linux","Command","Memo"]
---

# Linux Memo

## 画面操作

### ターミナル操作のショートカット

|Mac|Windows|description|
|---|---|---|
|```control``` + ```a```|```ctrl``` + ```a```|カーソルを行頭へ移動|
|```control``` + ```e```|```ctrl``` + ```e```|カーソルを行末へ移動|
|```control``` + ```f```|```ctrl``` + ```f```|カーソルを前進(右へ移動)|
|```control``` + ```b```|```ctrl``` + ```b```|カーソルを後退(左へ移動)|
|```control``` + ```d```|```ctrl``` + ```d```|カーソル上の1文字削除|
|```control``` + ```h```|```ctrl``` + ```h```|カーソル後方(左)の1文字削除|
|```control``` + ```u```|```ctrl``` + ```u```|カーソルから行頭まで削除|
|```control``` + ```k```|```ctrl``` + ```k```|カーソルから行末まで削除|
|```control``` + ```w```|```ctrl``` + ```w```|カーソル上の単語1つ分削除|
|```control``` + ```y```|```ctrl``` + ```y```|最後に削除した文字を貼り付け|
|```control``` + ```p```|```ctrl``` + ```p```|1つ前のコマンド履歴を表示|
|```control``` + ```n```|```ctrl``` + ```n```|1つ後のコマンド履歴を表示|
|```control``` + ```s```|```ctrl``` + ```s```|画面をロック|
|```control``` + ```q```|```ctrl``` + ```q```|画面のロックを解除|
|```control``` + ```c```|```ctrl``` + ```c```|実行中のコマンドを強制終了|
|```control``` + ```l(エル)```|```ctrl``` + ```l(エル)```|画面をクリア|
|```tab```|```tab```|コマンド,ディレクトリ,ファイルの候補を補完|


## 文字コード

### 文字コードの値

|文字コード|コマンドで指定する値|remarks|
|---|---|---|
|Shift_JIS|```cp932``` or ```sjis```|```cp932``` には ```sjis``` に含まれていない文字（①, ②, Ⅰ, Ⅱ, ㍉, ㌍, 彅, 髙 etc.）を含む。|
|EUC-JP|```euc-jp```|-|
|UTF-8|```utf-8```|-|

### 文字コードを設定

```.vimrc```
以下を記述し ```:wq``` で保存。
```bash
:set encoding=utf-8
:set fileencodings=utf-8,cp932,sjis,euc-jp
```

|long|short|description|
|---|---|---|
|```encoding```|```enc```|```vim``` の内部で使用されるエンコーディングを指定。|
|```fileencoding```|```fenc```|編集時のバッファファイルのエンコーディングを指定。encodingと異なる値が設定されていた場合、ファイルの読み書き時に文字コードを変換。fenc が空の場合、変換なし。|
|```fileencodings```|```fencs```|既存ファイル編集時の変換予定の文字コードを指定。カンマ区切りで複数記述可。

### vim 内部で使用される文字コードを確認
```bash
:set encoding?
```
or
```bash
:se enc?
```

### ファイルの文字コードを確認
```bash
:set fileencoding?
```
or
```bash
:se fenc?
```

### ファイルの文字コードの自動判別設定を確認
```bash
:set fileencodings?
```
or
```bash
:se fencs?
```

### 指定の文字コードでファイルを開き直し
```bash
:edit ++encoding=コマンドで指定する文字コードの値
```
or
```bash
:e ++enc=コマンドで指定する文字コードの値
```

### ファイルの文字コードを変換
以下を実行後に ```:w``` で保存すること。
```bash
:set fileencoding=コマンドで指定する文字コードの値
```
or
```bash
:set fenc=コマンドで指定する文字コードの値
```

### ファイルの文字コードを変換し保存
```bash
:setlocal fileencoding=コマンドで指定する文字コードの値
```
or
```bash
:setl fenc=コマンドで指定する文字コードの値
```

## 改行コード

### 改行コードの設定

```.vimrc```
以下を記述し ```:wq``` で保存。
```bash
:set fileformats=unix,dos,mac
```

### 改行コードの値

|改行コード|コマンドで指定する値|remarks|
|---|---|---|
|LF(Line Feed) ```\n```|```unix```|Unix, Linux, Mac(OS10以降), Android|
|CR(Carriage Return) ```\r```|```mac```|古いMac(OS9以前)|
|CRLF(Carriage Return/Line Feed) ```\r\n```|```dos```|Windows|

### 改行コードを指定
以下を実行後に ```:w``` で保存すること。
```bash
:set fileformat=コマンドで指定する改行コードの値
```
or
```bash
:se ff=コマンドで指定する改行コードの値
```

### 改行コードを指定し保存

```bash
:setlocal fileformat=コマンドで指定する改行コードの値
```
or
```bash
:setl ff=コマンドで指定する改行コードの値
```

## ディスク容量

### ディスク容量を確認
```bash
df -h --total
```
### 特定ディレクトリ配下の容量を確認
e.g) 容量が大きい順に30件表示
```bash
du -sh /path/to/target/directory/* | sort -hr | head -30
```

## 日付・時刻

```bash
TOMORROW=`date '+%Y%m%d' --date '1 day'`
YESTERDAY=`date '+%Y%m%d' --date '-1 day'`
echo ${TOMORROW}
echo ${YESTERDAY}
```
### ```date``` コマンドのオプション

|format|description|
|---|---|
|```%H```|時 (00～23)|
|```%I```|時 (01～12)|
|```%k```|時 ( 0～23)|
|```%l```|時 ( 1～12)|
|```%M```|分 (00～59)|
|```%p```|AM あるいは PM のロケール(国や地域に合わせた文字列)|
|```%r```|12時間形式の時刻 (HH:mm:ss [AP]M)|
|```%s```|1970-01-01 00:00:00 UTC からの秒数|
|```%S```|秒 (00～61)|
|```%T```|24時間形式の時刻 (HH:mm:ss)|
|```%a```|ロケールによる省略形の曜日の名前 (Sun～Sat)|
|```%A```|ロケールによる完全に表記した曜日の名前(Sunday～Saturday)|
|```%b```|ロケールによる省略形の月の名前 (Jan～Dec)|
|```%B```|ロケールによる完全に表記した月の名前(January～December)|
|```%c```|ロケールによる日付と時刻 (Sat Nov 04 12:02:33 EST 1989)|
|```%d```|日(月内通算日数) (01～31)|
|```%D```|日付 (MM/DD/YY)|
|```%j```|年内通算日数 (001～366)|
|```%m```|月 (01～12)|
|```%w```|週のうちの曜日(0～6)で0が日曜日に対応|
|```%x```|ロケールによる日付の表現 (MM/DD/YY)|
|```%y```|西暦の下2けた (00～99)|
|```%Y```|年 (1970～)|

## 検索

### ```grep``` コマンドの使用方法
```bash
grep 検索したい文字列 検索したいテキストファイル
```
任意のコマンドの実行結果に対して検索
```bash
コマンド | grep 検索したい文字列
```
複数のパターンを指定
```bash
grep -e 検索したい文字列1 -e 検索したい文字列2 検索したいテキストファイル
```
指定したディレクトリ配下を再帰的に検索, 検索結果に行番号を表示
```bash
grep -rn -e 'pattern' /target/dir/
```
### ```grep``` コマンドのオプション
|option|option|description|
|---|---|---|
|```-e 'パターン'```|```--regexp='パターン'```|パターンを指定して検索|
|```-f```|```--file=ファイル```|ファイルに記載したパターン(1行1パターン)を読み込み検索に使用|
|```-i```|```--ignore-case```|アルファベットの大文字と小文字を区別せず検索|
|```-w```|```--word-regexp```|単語全体でパターンと一致するものを検索|
|```-x```|```--line-regexp```|行全体がパターンと一致するものを検索|
|```-G```|```--basic-regexp```|基本正規表現(BRE)を検索に使用
|```-E```|```--extended-regexp```|拡張正規表現(ERE)を検索に使用|
|```-P```|```--perl-regexp```|Perl互換正規表現(PCRE)を検索に使用|
|```-v```|```--invert-match```|パターンに一致しない行を表示|
|```-s```|```--no-messages```|エラーメッセージを非表示|
|```-q```|```--quiet``` ```--silent```|結果を非表示(主にシェルスクリプトなどで判定用に使用|
|```-L```|```--files-without-match```|一致するものが含まれていないファイルのファイル名のみ表示|
|```-l```|```--files-with-matches```|一致するものが含まれているファイルのファイル名のみ表示|
|```-c```|```--count```|一致するものが含まれている回数のみ表示|
|```-m 回数```|```--max-count=回数```|パターンが指定した回数に達したら処理を終了|
|```-o```|```--only-matching```|一致した箇所だけを表示(通常は行単位で表示)|
|```-n```|```--line-number```|行番号を併せて表示(「行番号付きで表示」を参照)|
|```-b```|```--byte-offset```|パターンが何文字目にあるかを併せて表示|
|```-H```|```--with-filename```|ファイル名を併せて表示|
|```-h```|```--no-filename```|ファイル名を表示しない(複数ファイル指定時)|
||```--color=WHEN```|見つかったパターンを目立たせる(WHEN部分にはalways、never、autoを指定)|
|```-B 行数```|```--before-context=行数```|一致した行の前の行も表示(「前後の行も表示」を参照)|
|```-A 行数```|```--after-context=行数```|一致した行の後の行も表示(「前後の行も表示」を参照)|
|```-C 行数```,```-行数```|```--context=行数```|一致した行の前後の行も表示|
|```-r```|```--recursive```|ディレクトリを指定した場合はサブディレクトリ内のファイルも含め検索|

### タイムスタンプでの検索
#### 現在〜2日前(48時間前)
```bash
find ./ -mtime -2
```

#### 2日前(48時間前〜72時間前)
```bash
find ./ -mtime 2
```

#### 2日前(72時間前)〜過去
```bash
find ./ -mtime +1
```

e.g) 特定のディレクトリ内の30日以前のCSV拡張子のファイルを削除
```bash
find ./ -type f -name '*.csv' -mtime +30 | xargs rm -f
```

### ```find``` コマンドのオプション
|option|description|
|---|---|
|```-mmin```|ファイルのデータの最終更新日時（分指定）|
|```-mtime```|ファイルのデータの最終更新日時（日指定）|
|```-amin```|ファイルのデータの最終アクセス日時（分指定）|
|```-atime```|ファイルのデータの最終アクセス日時（日指定）|
|```-cmin```|ファイルのデータとステータスの最終更新日時（分指定）|
|```-ctime```|ファイルのデータとステータスの最終更新日時（日指定）|
|```-daystart```|00:00am 基準|


## 一覧表示

### タイムスタンプ降順で一覧表示(隠しファイル含む)
```bas
ls -alt
```
### タイムスタンプ昇順で一覧表示(隠しファイル含む)
```bas
ls -altr
```

### ```ls``` コマンドのオプション

|option|description|
|---|---|
|```-a```|先頭 ```.``` の隠しファイルも表示|
|```-A```|先頭 ```.``` の隠しファイル, 現在のディレクトリ ```.```, 親ディレクトリ ```..``` も表示|
|```-l(エル)```|詳細情報を表示|
|```-t```|タイムタンプ降順で表示|
|```-tr```|タイムタンプ昇順で表示|

## 移動・名称変更

※ 基本的にファイルもディレクトリも操作は同じ

### 1つのファイルを移動
e.g) ファイル ```sample.txt``` を ディレクトリ ```./dir/``` に移動
```bash
mv sample.txt ./dir/
```
### 複数のファイルを移動
e.g) ファイル ```sample1.txt``` と ```sample2.txt``` を ディレクトリ ```./dir/``` に移動
```bash
mv sample1.txt sample2.txt ./dir/
```
### ファイルの名称を変更(リネーム)
ファイル ```before.txt``` を ```after.txt``` に変更
```bash
mv before.txt after.txt
```

### ```mv``` コマンドのオプション
|option|option|description|
|---|---|---|
|```-v```|```--verbose```|実行内容を表示|
|```-i```|```--interactive```|移動元と移動先が同名の場合, 上書きする前に確認|
|```-b```|```--backup=方法```|移動元と移動先が同名の場合, 上書きされるファイル(移動先)のバックアップを作成｜
|```-S```|```--suffix=接尾語```|バックアップ作成時の接尾語を指定|
|```-n```|```--noclobber```|移動元と移動先が同名の場合, 上書きせず停止|
|```-u```|```--update```|移動元のファイルが移動先ファイルより新しいか新規ファイルの場合のみ上書き|


## コピー

※ 基本的にファイルもディレクトリも操作は同じ

### ファイルをコピー
```bash
cp -ip original.txt copy.txt
```
### ディレクトリごとファイルをコピー
```bash
cp -ipr original-dir copy-dir
```
### ファイルをコピーする際にバックアップを作成
```bash
cp -b --suffix=_$(date +%Y%m%d%H%M%S) original.txt copy.txt
```

### ```cp``` コマンドのオプション
|option|option|description|
|---|---|---|
|```-v```|```--verbose```|実行内容を表示|
|```-i```|```--interactive```|コピー元とコピー先が同名の場合, 上書きする前に確認|
|```-b```|```--backup=方法```|コピー元とコピー先が同名の場合, 上書きされるファイル(コピー先)のバックアップを作成｜
|```-S```|```--suffix=接尾語```|バックアップ作成時の接尾語を指定|
|```-u```|```--update```|コピー元のファイルがコピー先ファイルより新しいか新規ファイルの場合だけコピー|
|```-r```|```--recursive```|コピー元にディレクトリを指定した場合 再帰的にコピー|
|```-p```|```--preserve```|コピー元のパーミッションと所有者とタイムスタンプを保持（--preserve=mode,ownership,timestampsと同じ）|
|```-s```|```--symbolic-link```|シンボリック・リンクを作成|


### ネットワーク上のホスト間のSSHを利用したコピー

```bash
scp [option] [転送元ユーザー名@][転送元ホスト名:]転送元ファイルパス 転送先ユーザ名@転送先ホスト名:転送先パス
```
### ```scp``` コマンドのオプション
|option|description|
|---|---|
|```-r```|転送元のディレクトリ全体を再帰的にコピー|
|```-v```|転送の進行情報を表すメーターやデバッグ・メッセージを表示|
|```-q```|転送の進行情報を表すメーターを非表示|
|```-C```|通信を圧縮|
|```-p```|転送元の最終更新日時・最終アクセス日時・パーミッションを転送先に保持|
|```-P ポート番号```|転送先のポート番号を指定|
|```-i 秘密鍵ファイル```|秘密鍵ファイルを指定|


## ディレクトリの作成

### 多階層のディレクトリを作成

```bash
mkdir -p /dir/subdir/{subsubdir-1,subsubdir-2,subsubdir-3}
```
```bash
dir
└── subdir
    ├── subsubdir-1
    ├── subsubdir-2
    └── subsubdir-3
```

### 作成したディレクトリに作業場所を移動
```bash
mkdir -p /dir/subdir/subsubdir/ && cd $_
```

## ファイルの作成

### 空ファイルを作成
```bash
touch sample.txt
```

### ファイルのタイムスタンプを変更
e.g) ファイルのタイムスタンプ(最終更新日)を「2021-01-01 01:25:30」に変更
```bash
touch --date="2021-01-01 01:25:30" sample.txt
```
or
```bash
touch -t 202101012530.30 sample.txt
```

|short option|long option|description|
|---|---|---|
|```-t <time>```|-|[[CC]YY]MMDDhhmm[.ss] 形式で日時を指定|
|```-d```|-date=日付文字列|文字列で日時を指定|
|```-r```|--reference=ファイル名|指定したファイルと同じ日時を指定|




## ファイル操作

### ファイルの行数
#### ファイルの行数を出力
```bash
wc -l sample.txt
```

#### ファイルの空行の行数を出力
```bash
grep -c '^$' sample.txt
```

#### 指定した文字と一致しない行数を出力
```bash
grep -c -v 'hoge' sample.txt
```

### ファイル内容の並べ替え
#### 並べ替えた結果を出力
```bash
sort sample.txt
```
#### 並べ替え重複行は1行目のみを出力
```bash
sort -u sample.txt
```
```bash
sort --unique sample.txt
```

#### 並べられているか確認
```bash
sort -c
```
```bash
sort --check
```

### ファイル内容の重複

#### 重複行は1行目のみを出力
```bash
uniq sample.txt
```

#### 重複する行のみ出力
```bash
uniq -d sample.txt
```

### ファイル内容の正規表現での確認
#### 正規表現と一致する行数を確認
e.g.) 半角数字10桁のみの行数
```bash
grep -c -E '^[0-9]{10}$' sample.txt
```

## 圧縮

```zip```圧縮後に元ファイルを削除
```bash
zip -rm after.zip before.file
```



## CSV
### CSVファイルの各行のカラム数が指定した数(以下の例では 10)と一致しない行数を確認
```bash
cat sample.csv | awk -F ',' '{print NF}' | grep -c -v 10
```

### CSVファイルの特定のカラム(以下の例では10カラム目)のみを抽出
```bash
cut -d ',' -f 10 sample.csv > specific_column.csv
```

## システム情報/バージョン

### システム情報を確認
```bash
uname -a
```

### カーネルのバージョン情報を確認
```bash
cat /proc/version
```

### OSのバージョン情報を確認
```bash
cat /etc/os-release
```

### OSのバージョン情報を確認(ディストリビューション別)
※RHEL=Red Hat Enterprise Linux
|command                      |RHEL※|CentOS|Fedora|Amazon Linux|Oracle Linux|Debian|Ubuntu   |SUSE Linux|
|-----------------------------|-----|------|------|------------|------------|------|---------|----------|
|```cat /etc/system-release```|Yes  |Yes   |Yes   |Yes         |Yes         |      |         |          |
|```cat /etc/redhat-release```|Yes  |Yes   |Yes   |            |Yes         |      |         |          |
|```cat /etc/centos-release```|     |Yes   |      |            |            |      |         |          |
|```cat /etc/fedora-release```|     |      |Yes   |            |            |      |         |          |
|```cat /etc/oracle-release```|     |      |      |            |Yes         |      |         |          |
|```cat /etc/debian_version```|     |      |      |            |            |Yes   |Yes      |          |
|```cat /etc/lsb-release```   |     |      |      |            |            |      |Yes      |          |
|```cat /etc/SuSE-release```  |     |      |      |            |            |      |         |Yes       |


### OSのバージョン情報確認コマンドを確認
```bash
ls -l /etc/*release
```

## その他

### ログイン中のユーザー
```bash
LOGIN_USER=`who am i | awk -F ' ' '{print $1}'`
echo $LOGIN_USER
```

## References

* [インフラエンジニアとしてよく使うコマンド集](https://qiita.com/sion_cojp/items/04a2aa76a1021fe77079)
* [インフラエンジニアじゃなくても押さえておきたいSSHの基礎知識](https://qiita.com/tag1216/items/5d06bad7468f731f590e)
* [viの文字コードについて](http://capm-network.com/?tag=vi%E3%81%AE%E6%96%87%E5%AD%97%E3%82%B3%E3%83%BC%E3%83%89%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)
* [vimによる日本語文字コード自動判別と文字コード変換](https://itcweb.cc.affrc.go.jp/affrit/faq/tips/vim-enc)
* [grepでこういう時はどうする?](https://qiita.com/hirohiro77/items/771ffb64dddceabf69a3)
* [Man page of GREP](https://linuxjm.osdn.jp/html/GNU_grep/man1/grep.1.html)
* [gzip ファイルを操作](https://www.atmarkit.co.jp/ait/articles/1810/18/news014.html)