---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Memo
description: "H"
tags: ["Go", "Golang", "Memo"]
---

# Go Memo

### ゼロ値(型ごとの初期値)

|型|ゼロ値|
|---|---|
|数値(int や float65など)|0|
|string|""|
|bool|false|
|error|nil|


```go
package main

func main() {
	var sum int // ゼロ値 0 のため初期値の記述は不要
	for i := 1; i <= 10; i++ {
		sum += 1
	}
	println(sum)
}
```
```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	counts := map[string]int{}
	str := "dog dog dog cat dog"
	for _, s := range strings.Split(str, " ") {
		counts[s]++ // ゼロ値 0 のため初期値の記述は不要
	}
	fmt.Println(counts)
}
```


## コンポジット型
複数のデータ型が集まって1つのデータ型になっているもの

|型の種類|説明|ゼロ値|例|
|---|---|---|---|
|構造体|型の異なるデータ型を集めたデータ型|フィールドがすべてゼロ値||
|配列|同じ型のデータを集めて並べたデータ型|要素がすべてゼロ値||
|スライス|配列の一部を切り出したデータ型|nil||
|マップ|キーと値をマッピングさせたデータ型|nil||

構造体の例
```go
var person struct {
  name string
  age  int
}
```

```go
person := struct {
	name string
	age  int
}{
	name: "Gopher",
	age:  20,
}
fmt.Println(person.name)
```

配列の特徴と例
* 要素の型はすべて同じ
* 要素数が違えば別の型として扱われる
* 要素数は変更できない（要素数は固定長）
* 型は型リテラルで記述することが多い
```go
// ゼロ値で初期化
var ns1 [5]int
// 配列リテラルで初期化
var ns2 [5]{10, 20, 30, 40, 50}
// 要素数を値から推論
ns3 := [...]int{10, 20, 30, 40, 50}
// 要素の 5番目 50, 10番目 100, それ以外が0の要素数11の配列
ns4 := [...]int{5: 50, 10: 100}
fmt.Println(ns4[5]) // 5番目の値 50
fmt.Println(len(ns4)) // 要素数 11
```

スライスの例
```go
// ゼロ値は nil
var ns1 []int
// 長さと要素を指定
ns1 = make([]int, 3, 10) // type, length, capacity
// スライス・リテラルで初期化
// 要素数は指定しなくてOK
// 自動で配列は作成される
var ns2 = []int{10, 20, 30, 40, 50}
// 要素の 5番目 50, 10番目 100, それ以外が0の要素数11のスライス
ns3 := []int{5: 50, 10: 100}


ns := []int{10, 20, 30, 40, 50}
// 要素にアクセス
println(ns[3]) // 40
// 長さ
println(len(ns))
// 容量
println(cap(ns))
// 要素の追加
// 要素が足りない場合は背後の配列が確保される
ns = append(ns, 60, 70)
```

### コンポジット型を要素としてもつコンポジット型

* スライスの要素がスライスの場合（多次元スライス）
  * 例：```[][]int```
* マップの値がスライスの場合
  * 例：```map[string][]int```
* 構造体のフィールドの型が構造体

```go
struct {
  A struct {
    N int
  }
}
```

## ユーザ定義型

### ユーザ定義型の定義の方法
type で名前をつけて新しい型を定義する  
```type``` ```型名``` ```基底型```

```go
// 組み込み型を基にする
type MyInt int
// 他パッケージの型を基にする
type MyWriter io.Writer
// 型リテラルを基にする
type Person struct {
  Name string
}
```

### 基底型とユーザ定義型の相互キャストが可能
```go
type MyInt int
var n int = 100
m := MyInt(n)
n = int(m)
```

### 型なし定数から明示的なキャストは不要
デフォルトの型からユーザ定義型へキャストできる場合
```go
// 10秒を表す（time.Duration型）
d := 10 * time.Second
```
## コンパイルしてバイナリを生成
バイナリ（実行可能ファイル）の生成あり
```bash
$ go build main.go
$ go build .
$ go build pkgname
```

スライスの例
```go
var ns []int
```

マップの例
```go
var m map[string]int
// make で初期化
m = make(map[string]int)
// 容量を指定可
m = make(map[string]int, 10)
// リテラルで初期化
m := make(map[string]int{"x": 10, "y": 20})

// 空の場合
m := map[string]int{}
// キーをしていしてアクセス
println(m["x"])
// キーを指定して入力
m["x"] = 30

// 存在を確認
n, ok := m["z"]
println(z, ok)

// キーを指定して削除
delete(m, "z")

// 削除されていることを確認
n, ok := m["z"]
println(n, ok) // ゼロ値と false を返却

```
* キーと値の型を指定
* キーには ```==``` で比較できる型以外はNG。（スライスは不可。たぶん長さが異なる等があるため。）
  * キーとして構造体やポインタも指定可。構造体の要素がスライスの場合はNG。
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

### ローカル・パッケージのインポート
```text
sample/
├── foo/
│   └── bar.go
├── go.mod
├── go.sum
├── main.go
```

```go.mod```
```text
module sample

go 1.16
```

```foo/bar.go```
```go
package sample

import "fmt"

func IsAvailable() {
  fmt.Println("The local package is available.")
}
```

```main.go```
```go
package main

import "sample"

func main() {
  sample.IsAvailable()
}
```


## References
* https://docs.google.com/presentation/d/1RVx8oeIMAWxbB7ZP2IcgZXnbZokjCmTUca-AbIpORGk/edit#slide=id.g4f417182ce_0_0
* https://qiita.com/hnishi/items/a9217249d7832ed2c035
* https://qiita.com/fetaro/items/31b02b940ce9ec579baf
