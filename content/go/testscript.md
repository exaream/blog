---
date: 2022-08-07
lastmod: ["lastmod"]
title: Go TestScript
linkTitle: TestScript
description: Go TestScript
tags: ["Go", "Golang", "TestScript"]
---

# Go TestScript

## 概要
- [TestScript](https://cs.opensource.google/go/go/+/release-branch.go1.19:src/cmd/go/script_test.go) は 元々 Go のコンパイラをテストするために作成されたもの。
- ShellScript のように記述でき、ファイルシステム上で動作するものをテストできる。
- コンパイル時に使用される TestScript は以下で確認できる。  
  [https://cs.opensource.google/go/go/+/master:src/cmd/go/testdata/script/](https://cs.opensource.google/go/go/+/master:src/cmd/go/testdata/script/)
- [txtar](https://pkg.go.dev/golang.org/x/tools/txtar) (テキストベースのアーカイブ形式) によって表現されている。
- Go 内部のテストコードを使用できるよう、再構成されたものが以下で公開されている。  
  [https://github.com/rogpeppe/go-internal/tree/master/testscript](https://github.com/rogpeppe/go-internal/tree/master/testscript)



## 定義済コマンド

## テストコードの書き方

### 定義済コマンド一覧

- `cd`, `chmod`, `cp`, `env`, `exec`, `go`, `grep`, `mkdir`, `mv`, `rm`
- `exists`, `skip`, `sleep`, `stderr`, `stdout`, `stop`, `symlink`, `wait` etc
- [https://cs.opensource.google/go/go/+/release-branch.go1.19:src/cmd/go/script_test.go](https://cs.opensource.google/go/go/+/release-branch.go1.19:src/cmd/go/script_test.go;l=505-529)


### 定義済コマンドをテストで使用

testdata/script/sample.txt
```txt
exec echo 'hello world\n'
stdout 'hello world\n'
! stderr 'wrong sentence'
```

sample_test.go
```go {hl_lines=[2,14,15],linenostart=1}
package sample_test

import "github.com/rogpeppe/go-internal/testscript"

func TestFoo(t *testing.T) {
	testscript.Run(t, testscript.Params{
		Dir:         "testdata/script",
		WorkdirRoot: "testdata/script",
	})
}
```

### 独自のコマンドを作成してテストで使用

testdata/script/sample.txt
```txt
mycommand
stdout 'hello world\n'
! stderr 'wrong sentence'
```

```go
func TestMain(m *testing.M) {
    os.Exit(testscript.RunMain(m, map[string] func() int{
        "mycommand": func() int {
            fmt.Println("hello, world!")
            return 0 // the exit code to return (0 indicates success)
        },
    }))
}
```

## References
- https://cs.opensource.google/go/go/+/master:src/cmd/go/testdata/script/
- https://cs.opensource.google/search?q=testscript&ss=go%2Fgo:src%2F
- https://pkg.go.dev/github.com/rogpeppe/go-internal/testscript
- https://encore.dev/blog/testscript-hidden-testing-gem
