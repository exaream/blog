---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Memo
description: "H"
tags: ["Go", "Golang", "Memo"]
---

# Go Memo

## 変数

### ゼロ値(型ごとの初期値)

|型|ゼロ値|
|---|---|
|数値(int や float65など)|0|
|string|""|
|bool|false|
|error|nil|

## コンパイルしてバイナリを生成
バイナリ（実行可能ファイル）の生成あり
```bash
$ go build main.go
$ go build .
$ go build pkgname
```

## コンパイル, バイナリ
### コンパイルして実行
バイナリ（実行可能ファイル）の生成なし

```bash
$ go run main.go
$ go run .
$ go run pkgname
```

### コードのフォーマット（書式）を整形

```bash
$ go fmt main.go
$ go fmt .
$ go fmt pkgname
```

## 静的解析によるミスの検出
### バグと思われるミスを検出
```go test``` 実行時に自動で実行される
The Go Playground でも実行される
```bash
$ go vet main.go
$ go vet .
$ go vet pkgname
```
### エラー処理のミスを検出

```bash
$ go get github.com/kisielk/errcheck
```

```bash
$ errcheck ./...
```

## Linterのセットを使ってチェック
### Staticcheck
https://staticcheck.io/docs/  

```bash
$ go install honnef.co/go/tools/cmd/staticcheck@latest
$ staticcheck ./...
```

### GolangCI-Lint
https://golangci-lint.run/
```bash
$ brew install golangci-lint
$ brew upgrade golangci-lint
or
$ go get github.com/golangci/golangci-lint/cmd/golangci-lint@v1.42.0
```
```bash
$ golangci-lint run ./...
$ golangci-lint run dir1 dir2/... dir3/file1.go
```

### reviewdog
PRレビューで静的解析ツールを使用

```bash
$ brew install reviewdog/tap/reviewdog
$ brew upgrade reviewdog/tap/reviewdog
or
go install github.com/reviewdog/reviewdog/cmd/reviewdog@latest
```
```bash
golint ./...
```

## リファクタリング

* gomvpkg パッケージ名の変更ができるツール
  * https://golang.org/x/tools/cmd/gomvpkg
* gorename 識別子の名前を変更するツール
  * https://golang.org/x/tools/cmd/gorename
* eg exampleベースのリファクタリングツール
  * https://golang.org/x/tools/cmd/eg
  * https://rakyll.org/eg/

## Debug
* Delve Go専用のデバッガ, goroutine や channelにも対応
  * https://github.com/derekparker/delve
## File

### 拡張子を除外したファイル名を取得
```go
filepath.Base(path[:len(path)-len(filepath.Ext(path))])
```

## Struct
https://www.yoheim.net/blog.php?q=20170902  
構造体で、値メソッドとポインタメソッドを使い分ける

## Go Modules

https://qiita.com/hnishi/items/a9217249d7832ed2c035
https://qiita.com/fetaro/items/31b02b940ce9ec579baf

## Comment

### コメントが機能しない例
コメント内で ```--``` で始まる行を含む場合
```go
/*
-- foo bar --
*/
```

## References

* https://docs.google.com/presentation/d/1RVx8oeIMAWxbB7ZP2IcgZXnbZokjCmTUca-AbIpORGk/edit#slide=id.g4f417182ce_0_0