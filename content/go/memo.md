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
キャストなしの演算は不可
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

```go
type Duration int64
```

## 組み込み関数

|function|description|
|---|---|
|```print```|表示|
|```println```|表示(末尾の改行付き)|
|```make```|コンポジット型の初期化|
|```new```|指定した型のメモリの確保|
|```len```|スライスなどの長さを返却|
|```cap```|スライスなどの容量を返却|
|```copy```|スライスをコピー|
|```delete```|マップから指定したキーのエントリーを削除|
|```complex```|複素数型を作成|
|```imag```|複素数の虚部を取得|
|```real```|複素数の実数部を取得|
|```panic```|パニックを実行|
|```recover```|パニックから回復|

## 関数

### 関数の定義

```go
func add(x int, y int) int {
	return x + y
}

func swap(x, y int) (int, int) {
	return y, x
}

x, y := swap(10, 20)

// 戻り値の格納を省略したい場合は _ (ブランク変数)を使用
x, _ := swap(10, 20)
```

### 名前付き戻り値
* 名前付き戻り値は返却する値がわかりにくくなるため, あまり使わないこと
```go
func swap(x, y int) (x2, y2 int) {
	y2, x2 = x, y
	return
}
```

### 無名関数
* 無名関数を変数に格納したり戻り値として使用することも可能
* 無名関数と同じスコープ内の変数を無名関数内で使用可能（バグを生みやすいため要注意）
```go
package main

func main() {
	msg := "Hello, world"
	func() {
		println(mst) // 無名関数の外の変数を参照可能
	}() // 無名関数を定義し即呼び出し
}
```

### 関数型
* 変数への代入
* 引数に渡して使用
* 戻り値として返却

```go
fs := make([]func() string, 2) // string 型を返却する関数
// 関数を作成
fs[0] = func() string { return "hoge" }
fs[1] = func() string { return "fuga" }
for _, f := range fs {
	// 関数を実行
	fmt.Println(f())
}
```

### 無名関数のよくあるバグ

```go
fs := make([]func(), 3)
for i := range fs {
	fs[i] = func() { fmt.Println(i) }
}
// 上記のループを抜けた時点での i の値は 2 のため
// 以下のループで毎回 2 が出力される
for _, f := range fs {
	f()
}

// 期待値
// 0
// 1
// 2

// 実行結果
// 2
// 2
// 2
```
修正方法1
```go
fs := make([]func(), 3)
for i := range fs {
	i := i // ここを修正 ループのスコープの変数を格納
	fs[i] = func() { fmt.Println(i) }
}
for _, f := range fs {
	f()
}

// 実行結果
// 0
// 1
// 2
```

修正方法2
```go
fs := make([]func(), 3)
for i := 0; i < len(fs); i++ {
	func(i int) { fmt.Println(i) }(i)
}

// 実行結果
// 0
// 1
// 2
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
* [プログラミング言語Go完全入門](https://docs.google.com/presentation/d/1RVx8oeIMAWxbB7ZP2IcgZXnbZokjCmTUca-AbIpORGk/edit#slide=id.g4f417182ce_0_0)
* [Slice Tricks](https://i-beam.org/2019/12/09/go-slice-tricks/)
* https://qiita.com/hnishi/items/a9217249d7832ed2c035
* https://qiita.com/fetaro/items/31b02b940ce9ec579baf
