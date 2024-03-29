---
date: 2022-03-31
lastmod: ["lastmod"]
title: Go Check List
linkTitle: Check List
description: Go Check List
tags: ["Go", "Golang", "Check List"]
---

# Go Check Sheet

## References
- Style Guide
  - [Effective Go](https://go.dev/doc/effective_go)
  - [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)
  - [Uber Go Style Guide](https://github.com/uber-go/guide)
- Knowledge
  - [プログラミング言語Go完全入門](http://tenn.in/go)
  - [よくわかるcontextの使い方](https://zenn.dev/hsaki/books/golang-context)
  - [Goでの並行処理を徹底解剖！](https://zenn.dev/hsaki/books/golang-concurrency)
  - [Goで時刻を扱うチートシート](https://zenn.dev/hsaki/articles/go-time-cheatsheet)
  - [Goにおけるjsonの扱い方を整理・考察してみた ~ データスキーマを添えて](https://zenn.dev/hsaki/articles/go-convert-json-struct)
  - [Go言語のジェネリクス入門(1)](https://zenn.dev/nobishii/articles/type_param_intro)
  - [Go言語のジェネリクス入門(2) インスタンス化と型推論](https://zenn.dev/nobishii/articles/type_param_intro_2)
  - [Workspace mode](https://zenn.dev/kimuson13/articles/go-workspace-mode-impressions)
- Testing
  - [Advanced Testing with Go](https://speakerdeck.com/mitchellh/advanced-testing-with-go)
  - [Goのテーブル駆動テストをわかりやすく書きたい](https://zenn.dev/kimuson13/articles/go_table_driven_test)
- Library
  - [multierr](https://github.com/uber-go/multierr)
  - [testscript](https://github.com/rogpeppe/go-internal/tree/master/testscript)
- Static Analytics
  - [goleak](https://github.com/uber-go/goleak)
  - [errcheck](https://github.com/kisielk/errcheck)
  - [gosec](https://github.com/securego/gosec)
  - [tennvet](https://github.com/tenntenn/tennvet)
  - [fieldalignment](https://mattn.kaoriya.net/software/lang/go/20220127151742.htm)

## 設計
### パッケージ管理
- Go Modules (go.mod)

### 設計
- 分割
  - パッケージを適切に分割
  - ファイルを適切に分割
- 命名
  - 大文字なしの1単語
    - `UpperCamelCase`, `snake_cake`, `chain-case` は NG
  - 具体性のない命名は NG
    - e.g. `common` や `util` などは NG
    - `fileutil` のように具体性がある命名は許容範囲
  - 標準/準標準パッケージと同じ名前は避けること
  - 気持ちの良い命名は良い設計ができた証拠
  - 冗長な命名は設計がうまくいっていない証拠
- 適切に型定義すること
  - 多くの引数を引き回さないこと
  - 何でもかんでも構造体にしないこと
    - e.g. go/token.Pos int ソースコード上の場所を表す型　大小を演算子で比較できる
    　token.FileSet は構造体　構造体ではメソッド化が必要になるため面倒
  - 型同士がお互いを知りすぎていると密結合になるため注意
- 過度の抽象化をおこなわないこと
  - メソッドの数が多すぎるインターフェイス
  - テストの事情を背負いすぎているインターフェイス
- `internal` ディレクトリ
  - 公開したくない機能を隠蔽
  - `internal` 以下は内部パッケージ
  - `internal` ディレクトリ以下のパッケージのみ参照可
  - `internal` 以下でエクスポートしても外のパッケージから参照不可
- 大規模データの入力を考慮
  - 処理できる範囲を仕様として明確化
  - 大きすぎる入力はエラーとして処理 (大規模データの入力を想定せずに落ちるのはNG)
  - メモリが溢れないこと
  - 処理に時間がかかりすぎないこと
  - 再帰呼び出ししすぎてスタックオーバーフローをおこさないこと
- テスト可用性
  - パッケージ変数は極力使用しないこと t.Parallel 使用時に競合するため
  - Configurability(設定変更容易性) 定数は初期値としてのみ使用, 構造体のフィールド等で値を変更してテストで使用

## 実装
- README.md を作成
- 処理のまとまり
  - 読み手が脳のワーキングメモリを消費しない長さ
  - 読む際, 息継ぎしやすい長さ
  - ネストは浅く（特に if文 や for文）
- DRY
  - 同じ処理を複数回かかないこと
  - 適切に関数に切り出していること
- `main.go` はできるだけ薄くすること
- 標準/準標準パッケージの機能を充分に活用
  - `bufio`, `bytes`, `exec`, `fmt`, `fs`, `io`, `os`, `path`, `filepath`, `strings`, etc
- ディレクトリ区切り
  - Linux, Mac, Windows etc どのOSでも動作するようメソッドを選択
  - `path.Join` `path` パッケージの区切りは全てスラッシュ
  - `filepath.FromSlash`, `filepath.ToSlash`
- Export
  - メソッドと返り値で公開と非公開を統一（捻じれが生じないようにすること）
- 初期化
  - 初期化には make を使用したほうが間違いが少ない）
    `map[int]int{}` -> `make(map[int]int)`
- エラー処理
  - `log.Fatal` は内部で `os.Exit(1)` を呼んでいるためパッケージ側では使用しないこと
  - エラーメッセージ https://github.com/golang/go/wiki/CodeReviewComments#error-strings
    - 簡潔に記述
    - 先頭は小文字
    - 終端は句読点なし
  - 通常のエラー処理は panic を使用せず error を複数戻り値で使用　https://github.com/golang/go/wiki/CodeReviewComments#dont-panic
- 並行処理
  - 並行処理での競合を回避するためにパッケージ変数は基本的に使用せず不変なものにのみ使用すること
  - `context` のキーは 空の構造体（empty struct）を使用（メモリ消費なし）
  ```go
  type ctxKey struct{}
  var a ctxKey
  ```
## 解析


## テスト
- テーブル駆動テスト
  - 参照
    - https://zenn.dev/kimuson13/articles/go_table_driven_test
    - https://engineering.mercari.com/en/blog/entry/20211221-a-deep-dive-into-table-driven-testing-in-golang/
  - テーブルを簡潔に記述
    - テーブルのケースは可読性を考慮し横1行で記述
      - 名前の長い定数や変数を再定義
      - 型エイリアスを使用
      - デフォルト値を用意
      - ヘルパー関数を活用
        - ヘルパー関数はエラーを返却しないこと
    - 構造体リテラルからフィールド名を削除
    - 構造体の入力と期待値の間に1行空行を挿入（可読性を考慮）
    - 可変長引数をうまく活用した関数を作成　strings.NewReplacer
- エラー処理
  - t.Error と t.Fatal の使い分け
  - エラーがおきるかどうかは bool 値で判断
  - 関数の違いは `errors.As` や `errors.Is` を使用
  - 文言をチェックしたい場合は golden test で差分比較
- 非公開な機能(Unexported)
  - https://engineering.mercari.com/blog/entry/2018-08-08-080000/
- 並列化(Parallelization)
  - tt https://qiita.com/sho-hata/items/19356a3525fa47cfd2b1
  - t.Parallel 最初から極力全ての箇所で使用 https://engineering.mercari.com/blog/entry/how_to_use_t_parallel/
  - `go test -suffle ./...` テストを並列化できることをチェック
- 後処理
  - サブテスト関数で t.Parallel を使用する場合, defer ではなく Cleanup を使用 https://engineering.mercari.com/blog/entry/how_to_use_t_parallel/
- 出力先の切替
  - fmt.Fprintln(writer, xxx), io.Writer, os.Stdout, bytes.Buffer
- テストデータ
  - 可能であればテストヘルパーでデータを作成
  - 一時ディレクトリを使用 t.TempDir()
  - strings.Repeat 同じ文字列をリピートして生成
  - io.LimitedReader 指定したバイト数のデータを生成
  - golden ファイルテスト https://github.com/tenntenn/golden
  - txtar 形式(テキスター)を使用
- カバレッジ
  - `go test -cover ./...` カバレッジを出力
  - `go tool cover` テストを記述できていない箇所を視覚的に確認
- その他
  - `go test -v` は常時実行しないこと, CI等のノイズになるため, あくまでもオプション

## その他
- Timing-Dependent Tests https://speakerdeck.com/mitchellh/advanced-testing-with-go?slide=62
- 1行で Go Playground のURLを生成 `tree -Ffi --gitignore --noreport -I TODO.md | grep -v /$ | xargs gp share`
