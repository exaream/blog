---
date: 2020-01-01
lastmod: ["lastmod"]
title: VS Code
LinkTitle: VS Code
tags: ["Visual Studio Code","VS Code","Shortcut"]
---

# VS Code (Visual Studio Code)

## 設定

### 設定を開く
* Macの場合：```Code``` > ```Preferences...``` > ```Settings```

### ファイルを常に新しいタブで開く方法

* workbench.editor.enablePreview　を検索しチェックを解除

### プロジェクトを常に新しいウィンドウで開く方法
* window.openFoldersInNewWindow を検索し on を選択


## `code` command

### How to enable `code` command
- Use shortcut `⇧ ⌘ p` (shift + command + p)
- Select `Shell Command: Install command from PATH`

### High use commands

|Description|Command|
|---|---|
|Start VS Code|`code`|
|Open a directory with VS Code|`code .`|
|Open a file with VS Code|`code foo.txt`|
|Compare 2 files with VS Code|`code -d foo.txt bar.txt`|
|Display help|`code -h`|

## ショートカット

### 開く・閉じる
|内容|Mac|Windows|
|---|---|---|
|設定を開く|```command``` + ```,```|```ctrl``` + ```,```|
|コマンドパレットを開く|```command``` + ```shift``` + ```p```|```ctrl``` + ```shift``` + ```p```|
|新しいウィンドウを開く|```command``` + ```shift``` + ```n```|```ctrl``` + ```shift``` + ```n```|
|ファイル名を指定して開く|```command``` + ```p```|```ctrl``` + ```p```|
|アクティブなファイルを閉じる|```command``` + ```w```|```ctrl``` + ```w```|
|全てのファイル閉じる|```command``` + ```k``` ```w```|```ctrl``` + ```k``` ```w```|

### 拡大・縮小
|内容|Mac|Windows|
|---|---|---|
|拡大(ズームイン)|```command``` + ```=```|```ctrl``` + ```=```|
|縮小(ズームアウト)|```command``` + ```-```|```ctrl``` + ```-```|
|全画面表示|```command``` + ```control``` + ```-```|```F11```|

### コピー・切り取り・削除
|内容|Mac|Windows|
|---|---|---|
|行末尾のスペースを削除|```command``` + ```k``` ```x```|```ctrl``` + ```k``` ```x```|
|行のインデントを追加|```command``` + ```]```|```ctrl``` + ```]```|
|行のインデントを削除|```command``` + ```[```|```ctrl``` + ```[```|
|行のコピー(未選択時)|```command``` + ```c```|```ctrl``` + ```c```|
|行の切り取り(未選択時)|```command``` + ```x```|```ctrl``` + ```x```|
|ファイルの相対パスをコピー|```shift``` + ```option``` + ```command``` + ```c```|-|
|ファイルの絶対パスをコピー|```option``` + ```command``` + ```c```|-|
|コメント化/コメント解除|```option``` + ```shift``` + ```a```|```ctrl``` + ```/```|

### 選択
|内容|Mac|Windows|
|---|---|---|
|矩形選択|```shift``` + ```option``` + ```マウス・ドラッグ```|```shift``` + ```alt``` + ```マウス・ドラッグ```|

### 検索

|内容|Mac|Windows|
|---|---|---|
|プロジェクト全体を検索|```command``` + ```shift``` + ```f```|```ctrl``` + ```shift``` + ```f```|

### 移動
|内容|Mac|Windows|
|---|---|---|
|カーソル行を下に移動|```option``` + ```↓```|```alt``` + ```↓```|
|カーソル行を下に移動|```option``` + ```↑```|```alt``` + ```↑```|
|ファイル履歴を表示し移動|```control``` + ```tab```|```ctrl``` + ```tab```|
|サイドバーにフォーカスを移動|```command``` + ```0```|```ctrl``` + ```0```|
|指定行に移動|```control``` + ```g```|```ctrl``` + ```g```|
|最後に編集した箇所に移動|```command``` + ```k``` ```q```|```ctrl``` + ```k``` ```q```|
|対応するカッコに移動|```command``` + ```shift``` + ```\```|```ctrl``` + ```shift``` + ```¥```|
|前に移動(戻る)|```option``` + ```←```|```alt``` + ```←```|
|次に移動(進む)|```option``` + ```→```|```alt``` + ```→```|

## 参考
* https://qiita.com/TakahiRoyte/items/cdab6fca64da386a690b