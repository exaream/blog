---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Memo
description: "H"
tags: ["Go", "Golang", "Memo"]
---

# Go Memo

## macOS でパスを通す方法

```shell
echo "export GOPATH=$HOME/go" >> ~/.bash_profile
echo "export PATH=$PATH:$GOPATH/bin" >> ~/.bash_profile
source ~/.bash_profile
```

## コンパイル, バイナリ
### コンパイルして実行
バイナリ（実行可能ファイル）の生成なし

```shell
$ go run main.go
$ go run .
$ go run pkgname
```

### コンパイルしてバイナリを生成
バイナリ（実行可能ファイル）の生成あり
```shell
$ go build main.go
$ go build .
$ go build pkgname
```

## 静的解析によるミスの検出
### バグと思われるミスを検出
```go test``` 実行時に自動で実行される
The Go Playground でも実行される
```shell
$ go vet main.go
$ go vet .
$ go vet pkgname
```
### エラー処理のミスを検出

```shell
$ go get github.com/kisielk/errcheck
```

```shell
$ errcheck ./...
```

### セキュリティのチェック

```shell
$ curl -sfL https://raw.githubusercontent.com/securego/gosec/master/install.sh | sh -s -- -b $(go env GOPATH)/bin latest
```

```shell
$ gosec ./...
```
## Linterのセットを使ってチェック
### Staticcheck
https://staticcheck.io/docs/  

```shell
$ go install honnef.co/go/tools/cmd/staticcheck@latest
$ staticcheck ./...
```

### GolangCI-Lint
https://golangci-lint.run/
```shell
$ brew install golangci-lint
$ brew upgrade golangci-lint
or
$ go get github.com/golangci/golangci-lint/cmd/golangci-lint@v1.42.0
```
```shell
$ golangci-lint run ./...
$ golangci-lint run dir1 dir2/... dir3/file1.go
```

### reviewdog
PRレビューで静的解析ツールを使用

```shell
$ brew install reviewdog/tap/reviewdog
$ brew upgrade reviewdog/tap/reviewdog
or
go install github.com/reviewdog/reviewdog/cmd/reviewdog@latest
```
```shell
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

## コードのフォーマット
* コードの書式を整形
```shell
$ go fmt main.go
$ go fmt .
$ go fmt pkgname
```
## 組み込み型
言語に備わっているため最初から型のこと
|型|説明|ゼロ値(初期値)|
|---|---|---|
|bool|真偽値|false|
|string|文字列|""|
|int8/int16/int32/int64|整数(符号あり)|0|
|uint8/uint16/uint32/uint64|整数(符号なし=unsigned)|0|
|int|整数(符号あり, 32bit以上)|0|
|uint|整数(符号なし, 32bit以上)|0|
|byte|整数(uint8 の別名)|0|
|rune|整数(int32 の別名)|0|
|uintptr|整数(符号なし, ポインタの値を格納する大きさ)|0|
|float32/float64|浮動小数点数|0|
|complex32|複素数(float32の実数部と虚数部を持つ)|(0+0i)|
|complex64|複素数(float64の実数部と虚数部を持つ)|(0+0i)|
|error|エラー|nil|

## 変数定義

```go
// 変数定義と代入が同時
var n int = 100
// 変数定義と代入が別
var n int
n = 100
// 型を省略(型推論により自動的に int 型を定義)
var n = 100
// var を省略, 関数内でのみ使用可能
n := 100
// まとめて記述する方法
var (
	n = 100
	m = 200
)
```

## ゼロ値

* ゼロ値とはGoの変数に設定されている初期値のこと。
* 明示的な初期化をせずに使用可能。
### ゼロ値があるため初期化が不要な例

```go
package main

func main() {
	// ゼロ値 0 のため初期値の設定は不要
	// var sum int = 0
	var sum int
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
	words := []string{"dog", "cat", "dog", "fish", "cat"}
	counts := map[string]int{}
	for _, word := range words {
		// ゼロ値 0 のため初期値の設定は不要
		counts[word]++
	}
	fmt.Println(counts) // map[dog:2 cat:2 fish:1]
}
```

## 定数
* 定数とは値が変わらないもののこと。
* コンパイル時から値が変わらないもの。
* リテラルで記述されることが多い。

### リテラル

* 数字の区切り: 桁を区切る場合 ```_``` を使用 (可読性の向上のため)
* 2進数・8進数・16進数: 数字の前にプレフィックスを付与
|種別|値|説明||
|---|---|---|---|
|nilリテラル  |```nil```|無しを示す特別な値||
|真偽値リテラル|```true```|真||
|            |```false```|偽||
|数値リテラル  |```1234```|整数||
|            |```123_456```|整数 (カンマの変わりに _ を使用)|例は ```123,456``` と同義|
|            |```0777```|8進数||
|            |```0o755```|8進数|接頭辞は ```0O``` も可|
|            |```0x89ab```|16進数|接頭辞は ```0X``` も可|
|            |```0b1111```|2進数|接頭辞は ```0B``` も可|
|            |```123.4```|小数||
|            |```1.23e4```|浮動小数点数|```1.23E4``` も可|
|            |```1.23i```|虚数||
|文字列リテラル|```abc```|||
|ルーンリテラル|```a```|文字(rune)||

### 定数式
* 定数のみからなる演算式
* コンパイル時に計算

|種類|例|演算結果|
|---|---|---|
|四則演算|100 + 200|300|
|シフト演算|1 << 2|4|
|文字列結合|"Hello, " + "世界"|"Hello, 世界"|
|関係演算/論理演算|!(10 == 20)|true|

### 名前付き定数
* 定数に名前をつけて定義

```go
// 型のある定数
const n int = 100
// 型のない定数
const m = 100
// 定数式の使用
const s = "Hello, " + "世界"
// まとめて記述
const (
	x = 100
	y = 200
)
```

### 定数の型

* 型を明治しない場合, 定数は型を持たず, デフォルトの型を持つ

|種類|例|デフォルトの型|
|---|---|---|
|整数|100|int|
|浮動小数点数|1.5|float64|
|複素数|1+4i|complex128|
|ルーン|'A', '世', 'エ'|rune|
|文字列|"hoge"|string|
|真偽値|true, false|bool|

### 定数のデフォルトの型
* 型推論ではデフォルトの型が推論される
* 型が指定されている変数への代入はその型になる
* 変数や型のある定数との演算はそれらの型になる
  * キャストできない場合はコンパイルエラーになる

### 型定義の右辺の省略
名前付き定数定義の右辺は省略可
* グループ化された名前付き定数で使用
* 2つ目以降の名前付き定数定義の右辺が省略可
* 2つ目以降の定数定義の右辺は1つ目の右辺と同じ値
```go
func main() {
	const (
		a = 1 + 2
		b
		c
		d
	)
	fmt.Println(a, b, c, d) // 3 3 3 3
}
```

### iota
* 連続した定数を作るための仕組み
* グループ化された名前付き定数の定義で使用
* 0 から始まり1ずつ加算

```go
func main() {
	const (
		a = iota
		b
		c
		d
	)
	fmt.Println(a, b, c, d) // 0 1 2 3
}
```
```go
func main() {
	const (
		a = 1 << iota // 式の中でも使用可能
		b
		c
		d
	)
	fmt.Println(a, b, c, d) // 1 2 4 8
}
```


## 演算子

### 算術演算
|演算子|説明|備考|
|---|---|---|
|```+x```, ```-x```|符号||
|```x + y```|加算||
|```x - y```|減算||
|```x * y```|乗算(掛け算)||
|```x / y```|除算(割り算)||
|```x % y```|除算の余り||

### 代入演算
|演算子|説明|備考|
|---|---|---|
|```x = y```|変数への代入||
|```x := y```|変数の初期化と代入||
|```x++```|1の加算と代入|```x = x + 1``` と同義|
|```x--```|1の加算と代入|```x = x - 1``` と同義|
|```x += y```|加算と代入|```x = x + y``` と同義|
|```x -= y```|減算と代入|```x = x - y``` と同義|
|```x /= y```|除算(割り算)と代入|```x = x / y``` と同義|
|```x %= y```|除算(割り算)の余りの演算と代入|```x = x % y``` と同義|

### ビット演算
|演算子|説明|例|
|---|---|---|
|```x & y```|論理積(AND)||
|```x \| y```|論理和(OR)||
|```x ^ y```|排他的論理和(XOR)||
|```x &^ y```|x AND (NOT y)||
|```x << y```|左に算術シフト||
|```x >> y```|右に算術シフト||
|```x &= y```|論理積(AND)の演算と代入|```x = x & y``` と同義|
|```x \|= y```|論理和(OR)の演算と代入|```x = x \| y``` と同義|
|```x ^= y```|排他的論理和(XOR)の演算と代入|```x = x ^ y``` と同義|
|```x &^= y```|x AND (NOT y)の演算と代入|```x = x &^ y``` と同義|
|```x <<= y```|左に算術シフトと代入|```x = x << y``` と同義|
|```x >>= y```|右に算術シフトと代入|```x >> y``` と同義|


### 論理演算
|演算子|説明|備考|
|---|---|---|
|```x && y```|x かつ y (AND)||
|```x \|\| y```|x または y (OR)||
|```!x```|x が true の場合 false, false の場合 true (NOT)||

### 比較演算
|演算子|説明|備考|
|---|---|---|
|```x == y```|x と y が等しい||
|```x != y```|x と y が等しくない||
|```x < y```|x が y より小さい||
|```x <= y```|x は y 以下||
|```x > y```|x は y より大きい||
|```x >= y```|x は y 以上||

### アドレス演算

|演算子|説明|備考|
|---|---|---|
|```&x```|ポインタを取得||
|```*x```|ポインタが指す値を取得||

### チャネル演算
|演算子|説明|備考|
|---|---|---|
|```ch <- x```|チャネル ch に x を送信||
|```x =<- ch```|チャネル ch から x に受信||

## 制御構文

### 条件分岐 ```if```

```go
if x == 1 {
	println("x は 1")
} else if x == 2 {
	println("x は 2")
} else {
	println("x は 1 でも 2 でもない")
}
```
go の if 文の特徴
```go
// () は不要
if a == 0 {
}
// エラー (式の後に { なしの改行は不可)
if a == 0
{
}
// エラー (1行での記述は不可)
if (a == 0) println(a)
```

### 条件分岐 ```switch```
* ```break``` は不要
* ```case``` に式を使用可能

#### 式で判定する場合
```go
var age uint8 // 正の整数

switch {　// 式で判定する場合は引数の指定なし
case age <= 12:
	println("child")
	// 何もしなければ break
	// 次のケースに進みたい場合のみ fallthrough を記述
case age <= 19:
	println("teenager")
default:
	println("adult")
}
```

### 繰り返し(ループ) ```for```

```go
// 初期値; 継続条件; 更新文
if i := 0; i < 100; i++ {
}
// 継続条件のみ
if i <= 100 {
}
// 無限ループ
for {
}
// range を使用した繰り返し
for i, v := range []int{1, 2, 3} {
}
```

### ```break``` によるループの抜け出し

```go
func main() {
	var i int
	for {
		if i%2 == 1 {
			break
		}
		i++
	}
}
```

ラベル指定の ```break```

```go
func main() {
	var i int
LOOP:
	for {
		if i%2 == 1 {
			break LOOP
		}
		i++
	}
}
```

奇数と偶数
```go
package main

import (
	"fmt"
)

func main() {
	for i := 1; i <= 100; i++ {
		if i%2 == 0 {
			fmt.Printf("%d even\n", i)
		} else {
			fmt.Printf("%d odd\n", i)
		}
	}

	for i := 1; i <= 100; i++ {
		switch {
		case i%2 == 0:
			fmt.Printf("%d even\n", i)
		default:
			fmt.Printf("%d odd\n", i)
		}
	}
}
```
おみくじ
```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	t := time.Now().UnixNano() // current time
	rand.Seed(t)
	n := rand.Intn(6) // 0〜5

	switch n + 1 {
	case 6:
		fmt.Println("大吉")
	case 5, 4:
		fmt.Println("中吉")
	case 3, 2:
		fmt.Println("小吉")
	default:
		fmt.Println("凶")
	}
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

## 値のコピー

```go
p1 := struct {
	age  int
	name string
}{age: 10, name: "Gopher"}
p2 := p1 // コピー（変数への代入は実際にはコピー）
p2.age = 20
println(p1.age, p1.name) // 10 Gopher
println(p2.age, p2.name) // 20 Gopher
```

```go
package main

import "fmt"

type Person struct {
	Age  int
	Name string
}

func main() {
	p := Person{Age: 10, Name: "Gopher"}
	f(p) // p　をコピーして関数に渡している
	fmt.Println(p) // main関数内の p には影響なし
}

func f(p2 Person) {
	p2.Age = 100
}
```

## ポインタ

### ポインタとは
* 変数の格納先を表す値（メモリ上の番地のようなもの）
  * 「メモリ上にデータが書かれている範囲の先頭の位置」を格納した変数。型とともに利用することで、先頭の位置を起点にどれだけの大きさのメモリを読み取るかがわかる。
* 渡される型の値に対して破壊的な操作を加える際に利用
  * 破壊的な操作 = 関数を出てもその影響が残ること

```go
func f(xp *int) { // int のポインタ型
	*xp = 100 // * でポインタの指す先に値を入れる
}

func main() {
	var x int // 0
	f(&x) // & でポインタを取得し引き数として渡す
	println(x) // 100
}
```

### 内部でポインタを用いられているデータ型
以下の型はポインタを用いる必要がない場合が多い
* コンポジット型の一部
  * スライス
  * マップ
  * チャネル

### 安全にポインタを扱うためのGoの仕組み
#### ポインタを型とセットで安全に作成
  * Go では、ポインタ作成時に必ず型を併記することで、「ポインタ作成時点の型とアドレスの対応関係」がズレることがないようになっている。
#### ポインタ型を変換できない制約
  * 一度決定したポインタ型を他のポインタ型にキャストできないようにすることで、ポインタ作成後に不正な型に変換するミスを防止している。

### スタックとヒープ
* スタック：メモリの使い方や使用量がコンパイルに決定できる場合に用いられ、関数呼び出し時に確保され、関数から抜ける時に開放される。あらかじめどのメモリをどれだけ使うかわかっているため、必要になる直前で確保し、いらなくなったらすぐに履きでき、メモリを効率よく使える。
* ヒープ：メモリの使い方や使用量が実行時にしかわからない場合に用いる。ヒープに確保した変数の生存期間は用途によってバラバラなため、Goではガベージコレクタ（GC）を用いてヒープのメモリを集中管理し、しかるべきタイミングで開放している。
GCのアルゴリズムは複数ある。GoではGC時にSTW（Stop The World）が発生するものを採用している。
* 変数の型が実体で定義されている場合、Goはその変数をスタック上に確保される。
* 変数の型をポインタにする場合、メモリはヒープかスタックのどちらかに確保される。（ヒープに確保しなければいけない理由の1つは、変数の生存期間がわからないため。）

### エスケープ解析
* Goはポインタ変数であっても変数を作成した時点から利用しなくなるまで追跡可能であれば、メモリをヒープに確保しないよう最適化をおこなってくれる。
* エスケープ解析の結果は、-gcflags オプションをつけてビルドすることで確認できる
```go
go build -gcflags “-m”
```

### コピーと参照渡し
* Go では関数呼び出し時に引数や戻り値のコピーをおこなう。
* 変数をポインタにしていた場合、そのポインタが指すアドレス値をコピーする操作になる。
* 64ビットのCPUの場合は8バイト分のメモリ領域を新しく確保し、そこにアドレス値を書き込む。
* 変数を実体にしていた場合、アドレスではなくそこに書かれているデータ自体のコピーになる。
* フィールド数の多い構造体などをコピーする倍は多くのメモリを使用することになる。
* マップやスライスといったコンポジット型に対して値を出し入れする場合もコピーが発生する。
* これらの要素として大きなデータ構造を用いるとコピーのコストが無視できなくなる。
* 環境によっては、64バイトの大きさをもつ構造体を受け渡す場合とポインタの場合で3倍近くポインタのほうが高速になる。
* Goでは&を変数に付けることで明示的に参照を取得し、その結果を関数の引数として渡すことでコピーコストを抑えることができる。
* メソッドのレシーバも引数と同様にコピーされて渡されるため、レシーバもポイントとして定義したほうがコピーコストが抑えられる。
* 特別な理由がない場合、レシーバをポインタとして定義するほうが良い。


## メソッド
* レシーバと紐付けられた関数
  * データとそれに対する操作を紐付けるために使用
  * ドットでメソッドにアクセス

```go
type Hex int
func (h Hex) String() string {
	return fmt.Sprintf("%x", int(h))
}
```
```go
var hex Hex = 100
fmt.Println(hex.String()) // 64
```

### レシーバ
* メソッドに関連付けられた変数
  * メソッド呼び出し時は通常の引き数と同様の扱いとなり, コピーが発生
  * ポインタを用いることでレシーバへの変更を呼び出し元に伝えることが可能
  　　* レシーバが構造体の場合

```go
type T int
func (t *T) f() { println("hi") }

func main() {
	var v T = 1

	// 以下の2行は同じ意味
	// (型Tがメソッドを持っているわけではない)
	(&v).f() // ポインタをレシーバとして渡し v に対するメソッド f の振る舞いを実行
	v.f() // 上の行のシンタックスシュガー(簡略化した記述方法)
}
```

```go
package main

import "fmt"

type person struct {
	name string
	age  int
}

// レシーバとして構造体のポインタを渡し
// 構造体に対して破壊的変更をおこなう
func (p *person) rename(name string) {
	p.name = name
}

func main() {
	var p = person{name: "Foo", age: 20}
	fmt.Println(p.name) // Foo
	p.rename("Bar")
	fmt.Println(p.name) // Bar
}

```

```go
package main

type MyInt int

// レシーバとして構造体のポインタを渡し
// 構造体に対して破壊的変更をおこなう
func (n *MyInt) Inc() { *n++ }

func main() {
	var n MyInt
	println(n) // 0
	n.Inc()
	println(n) // 1
}
```

### ポインタ型のメソッドリスト

```go
type T struct{}
func (t T) f() {}
func main() {
	(T{}).f()   // T
	(&T{}).f()  // *T
	(*&T{}).f() // T
}
```
```go
type T struct{}
func (t *T) g() {}
func main() {
	(T{}).g() // 実行不可
	(&T{}).g()
	(*&T{}).g()
}

```

### レシーバとして使うことができる型

* type で定義した型(ユーザ定義の型)
* ポインタ型(レシーバに変更を与えたい場合)
* 内部にポインタを持つ型(マップやスライスなど)

* レシーバに対して append する場合はレシーバとしてポインタを渡すこと

### メソッド値

```go
type Hex int
func (h Hex) String() string {
	return fmt.Sprintf("%x", int(h))
}

func main() {
	var h Hex = 100

	// String() を実行するのではなく Stringメソッドを
	// f に格納するため String の後に () がついていない
	f := h.String // 変数.メソッド名  型は「func() string」
	fmt.Println(f()) // String() を実行
}
```

### メソッド式
* メソッドを表す式
* レシーバを第1引き数とした関数になる

```go
type Hex int
func (h Hex) String() string {
	return fmt.Sprintf("%x", int(h))
}

func main() {
	var h Hex = 100
	f := Hex.String // 変数の型.メソッド名  レシーバの値を決定していない状態でメソッドを参照
	fmt.Printf("%T\n%s\n", f, f(h)) // レシーバを第1引き数とした関数
}
```

## パッケージ

### パッケージとは
* 関数・変数・定数・型を意味のある単位でまとめたもの
* 複数のファイルで構成可能
  * 型の定義とメソッドの定義が別ファイルに記述可
  * 同じディレクトリ内でパッケージが混在するのはNG
  * ```import``` 文はフィルごとに記述

### パッケージの種類
* main パッケージ
  * main 関数が存在するパッケージ
  * プログラムの起点(エントリポイント)となるパッケージ
  * 実行可能なGoプログラムの場合は必ず存在
* 標準パッケージ
  * Go が最初から用意しているパッケージ
  * 100以上のパッケージが存在
* サードパーティパッケージ
  * 第三者(自分を含む)が開発したパッケージ
  * インターネット上に公開されている場合が多い
  * インストールすることで使用可能
  * ライブラリとも呼ばれている

### パッケージのインポート
* 他のパッケージに用意された機能(変数・関数・定数など)を使用可

```shell
$ go get <pkgname>
```
e.g.
```shell
$ go get github.com/xxx/xxx
```

### パッケージ名のエイリアス
* インポートパスの左側にエイリアスを記述
* パッケージが他のパッケージと衝突している場合にエイリアスを使用
* インポートパスとパッケージ名が異なる場合にエイリアスを使用(バージョンの場合など)
```go
import (
	"fmt"
	"log"

	mylog "github.com/foo/log" // パッケージが他のパッケージと衝突している場合
	bar "github.com/foo/bar/v2" // インポートパスとパッケージ名が異なる場合
)
```

### パッケージ外へのエクスポート
* 先頭大文字にした識別子がエクスポートの対象 (=他パッケージから参照可)

```go
var Foo string // exported
var bar string // unexported
```
### GOPATH
* ソースコードやビルドされたファイルを配置するパス
GOPATH の確認方法
```shell
$ go env GOPATH
```

```shell
tree -L $GOPATH
go
├── bin
└── pkg
    ├── mod
    └── sumdb
        └── sum.golang.org
```


## ディレクトリ/ファイル

### パスを結合
```go
path := filepath.Join("dir", "main.go")
```

### 相対パスを取得
```go
relativePath, err := filepath.Rel(base_path, target_path)
```

### 相対パスから絶対パスを取得
```go
absolutePath, err := filepath.Abs(relative_path)
```

## ディレクトリ

### ディレクトリ名を取得
```go
dirName := filepath.Dir(path)
```
### ディレクトリを作成
```go
err := os.Mkdir("foo", 0755)
```

### ディレクトリを再帰的に作成
```go
err := os.MkdirAll("foo/bar", 0755)
```

### ディレクトリ名を変更

```go
err := os.Rename("before", "after")
```

### ディレクトリを移動
* ```osRename()``` で移動
* パーテーションをまたぐ場合は移動不可
```go
err := os.Rename("foo/bar/baz", "foo/baz")
```

### ディレクトリの権限を変更
```go
err := os.Chmod("foo", 0755)
```

### ディレクトリを削除
```go
err := os.Remove("foo")
```

### ディレクトリを再帰的に削除
* ディレクトリ内のサブディレクトリやファイルも削除
```go
err := os.RemoveAll("foo")
```


## ファイル

### ファイルを作成
```go
fileObj, err := os.Create("foo.txt")
```

### ファイルに入力
```go
byteCount, err := fileObj.WriteString("Hello\n")
```

### ファイルの情報を取得
```go
fileInfo, err := os.Stat(file_name)
// or
fileInfo, err := file_obj.Stat()

fileInfo.Name() // ファイル名
fileInfo.Size() // ファイルサイズ
fileInfo.Mode() // ファイルモード
fileInfo.ModTime() // 最終更新日時
fileInfi.IsDir() // ディレクトリか否か
```

### ファイル名を取得
```go
fileName := filepath.Base(path)
```

### ファイルの拡張子を取得
```go
fileExt := filepath.Ext(path)
```

### ファイルの拡張子を除外したファイル名を取得
```go
fileStem := filepath.Base(path[:len(path)-len(filepath.Ext(path))])
```

### ファイル名を変更
```go
err := os.Rename("foo.txt", "bar.txt")
```

### ファイルを移動
```go
err := os.Rename("foo/bar/baz.txt", "foo/baz.txt")
```

### ファイルの権限を変更
```go
err := os.Chmod("foo.txt", 0644)
```

## 日付・時刻


### 現在日時を取得
```go
t := time.Now()
fmt.Println(t) // 2021-08-27 23:44:43.784975 +0900 JST m=+0.000236593
```

### タイムゾーンを指定し日時を取得
```go
local := time.Date(2021, 1, 2, 3, 4, 5, 6, time.Local)
fmt.Println(local) // 2021-01-02 03:04:05.000000006 +0900 JST
utc := time.Date(2021, 1, 2, 3, 4, 5, 6, time.UTC)
fmt.Println(utc)   // 2021-01-02 03:04:05.000000006 +0000 UTC
```

### タイムゾーンを変換
```go
t := time.Date(2021, 1, 2, 3, 4, 5, 6, time.Local)
fmt.Println(t)     // 2021-01-02 03:04:05.000000006 +0900 JST
utc := t.UTC() 
fmt.Println(utc)   // 2021-01-01 18:04:05.000000006 +0000 UTC
local := utc.Local()
fmt.Println(local) // 2021-01-02 03:04:05.000000006 +0900 JST
```

```go
const ymdhis string = "2006-01-02 15:04:05"

now := time.Now()
// UTC (協定世界時)
utc, err := time.LoadLocation("UTC")
if err != nil {
	log.Println("Failed to load location UTC.")
}
fmt.Println(utc.String())               // UTC
fmt.Println(now.In(utc).Format(ymdhis)) // 2021-01-01 18:04:05
// JST (日本標準時)
jst, err := time.LoadLocation("Asia/Tokyo")
if err != nil {
	log.Println("Failed to load location Asia/Tokyo.")
}
fmt.Println(jst.String())               // Asia/Tokyo
fmt.Println(now.In(jst).Format(ymdhis)) // 2021-01-02 03:04:05
```

Local -> UTC
```go
const ymdhis string = "2006-01-02 15:04:05"

t, err := time.ParseInLocation(ymdhis, "2021-01-02 03:04:05", time.Local)
if err != nil {
	log.Println("Faild to parse local time.")
}
t.In(time.UTC)
```
UTC -> Local
```go
const ymdhis string = "2006-01-02 15:04:05"

t, err := time.ParseInLocation(ymdhis, "2021-01-02 03:04:05", time.UTC)
if err != nil {
	log.Println("Faild to parse time of UTC.")
}
t.In(t.Local)
```

### 年月日・時分秒・ナノ秒を取得
```go
t := time.Date(2021, 1, 2, 3, 4, 5, 6, time.Local)
fmt.Println(t.Year())       // 2021
fmt.Println(t.Month())      // January
fmt.Println(int(t.Month())) // 1
fmt.Println(t.Day())        // 2
fmt.Println(t.Weekday())    // Saturday
fmt.Println(t.YearDay())    // 2
fmt.Println(t.Hour())       // 3
fmt.Println(t.Minute())     // 4
fmt.Println(t.Second())     // 5
fmt.Println(t.Nanosecond()) // 6
```

### 年月日を取得
```go
t := time.Date(2021, 1, 2, 3, 4, 5, 6, time.Local)
year, month, day := t.Date()
fmt.Println(year)  // 2021
fmt.Println(month) // January
fmt.Println(day)   // 2
```

### 時分秒を取得
```go
t := time.Date(2021, 1, 2, 3, 4, 5, 6, time.Local)
hour, min, sec := t.Clock()
fmt.Println(hour) // 3
fmt.Println(min)  // 4
fmt.Println(sec)  // 5
```

### UNIXタイム・秒・ミリ秒・ナノ秒を取得
```go
t := time.Date(2021, 1, 2, 3, 4, 5, 6, time.Local)
fmt.Println(t.Unix())                               // 1609524245
fmt.Println(t.UnixNano() / int64(time.Millisecond)) // 1609524245000
fmt.Println(t.UnixNano())                           // 1609524245000000006
```

### 時・分・秒・ミリ秒以下の切り捨て
```Trancate()``` での切り捨ては ```UTC``` で行われるとのこと
```go
t := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.UTC)
fmt.Println(t)                          // 2021-01-02 03:04:05.6789 +0000 UTC (切り捨て前)
fmt.Println(t.Truncate(time.Hour * 24)) // 2021-01-02 00:00:00 +0000 UTC (切り捨て 時間)
fmt.Println(t.Truncate(time.Hour))      // 2021-01-02 03:00:00 +0000 UTC (切り捨て 分)
fmt.Println(t.Truncate(time.Minute))    // 2021-01-02 03:04:05 +0000 UTC (切り捨て 秒)
fmt.Println(t.Truncate(time.Second))    // 2021-02-03 04:05:06 +0000 UTC (切り捨て ミリ秒以下)
t = time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.Local)
fmt.Println(t.Truncate(time.Hour * 24)) // 2021-01-01 09:00:00 +0900 JST (UTC で切り捨て済)
```

### 日時の追加(単位を使用)
```go
t1 := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.UTC)
fmt.Println(t1)               // 2021-01-02 03:04:05.6789 +0000 UTC
t2 := t1.Add(time.Minute * 2) // 2分追加
fmt.Println(t2)               // 2021-01-02 03:06:05.6789 +0000 UTC
```

### 日時の追加(追加する年・月・日を指定)
```go
t1 := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.UTC)
fmt.Println(t1)           // 2021-01-02 03:04:05.6789 +0000 UTC
t2 := t1.AddDate(1, 2, 3) // 1年・2ヶ月・3日追加
fmt.Println(t2)           // 2022-03-05 03:04:05.6789 +0000 UTC
```

### 日時の前後比較
```go
// t1 < t2
t1 := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.UTC)
t2 := time.Date(2021, 1, 2, 3, 4, 5, 678910000, time.UTC)

fmt.Println(t1.Before(t2)) // true
fmt.Println(t2.Before(t1)) // false
fmt.Println(t1.After(t2))  // fase
fmt.Println(t2.After(t1))  // true
```

### 日時の同値比較
```go
// t1 == t2
t1 := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.UTC)
t2 := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.UTC)

fmt.Println(t1.Equal(t2)) // true
```

### 現在時刻までの経過時間を取得

```go
t := time.Date(2021, 1, 2, 3, 4, 5, 678900000, time.Local)
d := time.Since(t) // or time.Now().Sub(t)
fmt.Println(d) // 5800h26m17.806336s
```
### 曜日を日本語に変換
```go
weekdays := [...]string{"日", "月", "火", "水", "木", "金", "土"}
now := time.Now()
weekday := now.Weekday()
fmt.Println(weekday)           // =>"Tuesday"
fmt.Println(weekdays[weekday]) // =>"火"
```

## IPアドレス

### IPアドレスからホスト名を取得
```go
host, err := net.LookupAddr(ip)
```

## Slice

スライスを比較
https://qiita.com/Sekky0905/items/1ff4979d80b163e0aeb6
```go
reflect.DeepEqual(slice1, sclice2)
```
## Struct
構造体で、値メソッドとポインタメソッドを使い分ける
https://www.yoheim.net/blog.php?q=20170902  

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

## Command

### パイプで渡ってきた値を処理
https://kaneshin.hateblo.jp/entry/2016/07/05/004601
```go
package main

import (
	"fmt"
	"io/ioutil"
	"os"
)

func main() {
	body, _ := ioutil.ReadAll(os.Stdin)
	fmt.Println(string(body))
}
```
`ls` の結果を Go で出力
```shell
$ ls | go run main.go
```

## メモ

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


## References
* [プログラミング言語Go完全入門](https://docs.google.com/presentation/d/1RVx8oeIMAWxbB7ZP2IcgZXnbZokjCmTUca-AbIpORGk/edit#slide=id.g4f417182ce_0_0)
* [Slice Tricks](https://i-beam.org/2019/12/09/go-slice-tricks/)
* https://www.tohoho-web.com/ex/golang.html
* https://ashitani.jp/golangtips/tips_file.html
* https://qiita.com/hnishi/items/a9217249d7832ed2c035
* https://qiita.com/fetaro/items/31b02b940ce9ec579baf
* https://qiita.com/tchnkmr/items/f3c94abb3e3a47e993ab
