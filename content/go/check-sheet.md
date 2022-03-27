---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Memo
description: ""
tags: ["Go", "Golang", "Check Sheet"]
---

# Go Check Sheet

## 設計
- パッケージ管理
  - Go Modules (go.mod)
  - Workspace mode https://zenn.dev/kimuson13/articles/go-workspace-mode-impressions
- 分割
  - ファイルを適切に分割
    - main.go は極力薄くすること
  - パッケージを適切に分割
- 命名
  - 1単語
  - 大文字なし
  - 気持ちの良い命名は良い設計ができた証拠
  - 長ったらしい説明有頂な名前は設計がうまくいっていない証拠
- 適切に型定義すること
  - 多くの引数を引き回さないこと
  - 何でもかんでも構造体にしないこと
  - 型同士がお互いを知りすぎていると密結合になるため注意
- 過度の抽象化をおこなわないこと
  - メソッドの数が多すぎるインターフェイス
  - テストの事情を背負いすぎているインターフェイス
- internal ディレクトリ
  - 公開したくない機能を隠蔽
  - internal 以下は内部パッケージ
  - internal ディレクトリ以下のパッケージのみ参照可
  - internal 以下でエクスポートしても外のパッケージから参照不可
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
- 標準/準標準パッケージの機能を充分に活用
  - `bufio`
  - `bytes`
  - `exec`
  - `fmt`
  - `fs`
  - `io`
  - `os`
  - `path`, `filepath`
  - `strings`
- ディレクトリ区切り
  - Linux, Mac, Windows etc どのOSでも動作するようメソッドを選択
  - `path.Join` `path` パッケージの区切りは全てスラッシュ
  - `filepath.FromSlash`, `filepath.ToSlash`
- エラー処理
  - `log.Fatal` は内部で `os.Exit(1)` を呼んでいるためパッケージ側では使用しないこと
  - エラーメッセージ https://github.com/golang/go/wiki/CodeReviewComments#error-strings
    - 簡潔に記述
    - 先頭は小文字
    - 終端は句読点なし
  - 通常のエラー処理は panic を使用せず error を複数戻り値で使用　https://github.com/golang/go/wiki/CodeReviewComments#dont-panic

## 解析
- エラー処理 errcheck https://github.com/kisielk/errcheck
- セキュリティ gosec https://github.com/securego/gosec
- 他 tennvet https://github.com/tenntenn/tennvet

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
```go
cases := map[string]struct {
	foo string
	bar int

  want string
}{
```
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
- ssh.IsTerminal
- Timing-Dependent Tests https://speakerdeck.com/mitchellh/advanced-testing-with-go?slide=62
- 1行で Go Playground のURLを生成 `tree -Ffi --gitignore --noreport -I TODO.md | grep -v /$ | xargs gp share`
