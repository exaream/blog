---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Memo
description: "H"
tags: ["Go", "Golang", "Memo"]
---

# Go Memo

## コンパイル, バイナリ
### コンパイルして実行
バイナリ（実行可能ファイル）の生成なし

```bash
$ go run main.go
$ go run .
$ go run pkgname
```

### コンパイルしてバイナリを生成
バイナリ（実行可能ファイル）の生成あり
```bash
$ go build main.go
$ go build .
$ go build pkgname
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

## コードのフォーマット
* コードの書式を整形
```bash
$ go fmt main.go
$ go fmt .
$ go fmt pkgname
```
## 組み込み型
言語に備わっているため最初から型のこと
|型|概要|ゼロ値(初期値)|
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

|種類|例|
|---|---|
|数値リテラル|100, 1.5, 1+4i|
|文字列リテラル|"foo", "bar"|
|ルーンリテラル|'A', '世', 'エ'|
|真偽値リテラル|true, false|

### 2進数・8進数・16進数表記
* 10進数以外にも対応
* 数字の前にプレフィックスを付与

|10進数|2進数|8進数|16進数|
|---|---|---|---|
|100|0b1100100 (Go1.13)|0144, 0o144 (Go1.13)|0x64|
|10.5|-|-|0x1.5p+03 (Go1.13)|

### 数字の区切り
* 桁を区切りたい場合に使用
```go
5_000_000_000_000_000
```
```go
0b_1010_0110
```
```go
3.1415_9265
```

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
|演算子|説明|例|
|---|---|---|
|```+```, ```-```|符号|+100, -100|
|```+```|加算|2+1|
|```-```|減算|2-1|
|```/```|割り算|4/2|
|```*```|掛け算|5*3|
|```%```|余り|5%2|

### 代入演算
|演算子|説明|例|
|---|---|---|
|```=```|変数への代入|a = 100|
|```:=```|変数の初期化と代入|a := 100|
|```+=```, ```-=``` 等|演算と代入|i += 2|
|```++```|i += 1 と同じ|i++|
|```--```|i -= 1 と同じ|i--|

### ビット演算
|演算子|説明|例|
|---|---|---|
|```\|```|論理和|0x10|0x01|
|```&```|論理積|0x1&0xf|
|```^```|否定|^0x3|
|```^```|排他的論理和|0xc^0x3|
|```&^```|論理積の否定|0xc&^0x3|
|```<<```|左に算術シフト|0x1<<4|
|```>>```|右に算術シフト|0x1>>4|

### 論理演算
|演算子|説明|例|
|---|---|---|
|```\|\|```|または| a \|\| b|
|```&&```|かつ|a && a|
|```!```|否定|!a|

### 比較演算
|演算子|説明|例|
|---|---|---|
|```==```|等しいかどうか|a == b|
|```!=```|等しくないか|a != b|
|```<```|a は b より小さい|a < b|
|```<=```|a は b 以下|a <= b|
|```>```|a は b より大きい|a > b|
|```>=```|a は b 以下|a >= b|

### アドレス演算

|演算子|説明|例|
|---|---|---|
|&|ポインタを取得|&a|
|*|ポインタが指す値を取得|*a|

### チャネル演算
|演算子|説明|例|
|---|---|---|
|```<-```|チャネルへの送受信|```ch<-100```, ```<-ch```|

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

```go
switch age {
case 0, 1, 2: // 0 または 1 または 2 の場合
	println("baby")
	// 何もしなければ break
	// 次のケースに進みたい場合のみ fallthrough を記述
case a >= 3 && a <= 12: // 式を使用する場合
	println("child")
case a >= 13 && a <= 19: // 式を使用する場合
	println("teenager")
case a > 20: // 式を使用する場合
	println("adult")
default:
	println("default")
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
* 変数の格納先を表す値（メモリ上の番地のようなもの）
* 値で渡される型の値に対して破壊的な操作を加える際に利用
（破壊的な操作 = 関数を出てもその影響が残ること）

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

## File

### パスを結合
```go
path := filepath.Join("dir", "main.go")
```

### ディレクトリ名を取得
```go
dir_name := filepath.Dir(path)
```

### ファイル名を取得
```go
file_name := filepath.Base(path)
```

### 拡張子を取得
```go
file_ext := filepath.Ext(path)
```

### 拡張子を除外したファイル名を取得
```go
file_stem := filepath.Base(path[:len(path)-len(filepath.Ext(path))])
```

### 相対パスを取得
```go
relative_path := filepath.Rel(base_path, target_path)
```

### 相対パスから絶対パスを取得
```go
absolute_path := filepath.Abs(relative_path)
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
* https://qiita.com/hnishi/items/a9217249d7832ed2c035
* https://qiita.com/fetaro/items/31b02b940ce9ec579baf