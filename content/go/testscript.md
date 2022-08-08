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
- TestScript は元々 Go のコンパイラをテストするために作成されたもの。  
  https://cs.opensource.google/go/go/+/release-branch.go1.19:src/cmd/go/script_test.go
- シェルスクリプトのように記述でき、ファイルシステム上で動作するものをテストできる。
- 定義済のコマンドは以下で確認できる。  
  [https://cs.opensource.google/go/go/+/release-branch.go1.19:src/cmd/go/script_test.go](https://cs.opensource.google/go/go/+/release-branch.go1.19:src/cmd/go/script_test.go;l=505-529)
- 独自のコマンドを追加することができる。
- Go のコンパイル時に使用される TestScript は以下で確認できる。  
  [https://cs.opensource.google/go/go/+/master:src/cmd/go/testdata/script/](https://cs.opensource.google/go/go/+/master:src/cmd/go/testdata/script/)
- TestScript は txtar (テキストベースのアーカイブ形式) によって表現されている。  
  [https://pkg.go.dev/golang.org/x/tools/txtar](https://pkg.go.dev/golang.org/x/tools/txtar)
- Go 内部のテストコードを使用できるよう、再構成されたものが以下で公開されている。  
  [https://github.com/rogpeppe/go-internal/tree/master/testscript](https://github.com/rogpeppe/go-internal/tree/master/testscript)


## テストコードの書き方

### 定義済コマンドをテストで使用

testdata/script/TestFoo/hello-world.txt
```txt
exec echo 'hello world!'
stdout 'hello world!\n'
```

sample_test.go
```go {hl_lines="14-17",linenostart=1}
package sample_test

import (
	"path/filepath"
	"testing"

	"github.com/rogpeppe/go-internal/testscript"
)

var scriptDir = filepath.Join("testdata", "script")

func TestFoo(t *testing.T) {
	t.Parallel()
	testscript.Run(t, testscript.Params{
		Dir:         filepath.Join(scriptDir, t.Name()),
		WorkdirRoot: t.TempDir(),
	})
}
```

### 独自のコマンドを作成してテストで使用

testdata/script/TestFoo/hello-world.txt
```txt {hl_lines=1,linenostart=1}
mycat hello.txt world.txt
stdout 'hello\n'
stdout 'world!\n'

-- hello.txt --
hello
-- world.txt --
world!
```
sample_test.go
```go {hl_lines="13-17",linenostart=1}
package sample_test

import (
	"os"
	"path/filepath"
	"testing"

	"github.com/rogpeppe/go-internal/testscript"
)

var scriptDir = filepath.Join("testdata", "script")

func TestMain(m *testing.M) {
	os.Exit(testscript.RunMain(m, map[string]func() int{
		"mycat": mycat,
	}))
}

func TestFoo(t *testing.T) {
	t.Parallel()
	testscript.Run(t, testscript.Params{
		Dir:         filepath.Join(scriptDir, t.Name()),
		WorkdirRoot: t.TempDir(),
	})
}
```

## References
- https://cs.opensource.google/go/go/+/master:src/cmd/go/testdata/script/
- https://cs.opensource.google/search?q=testscript&ss=go%2Fgo:src%2F
- https://pkg.go.dev/github.com/rogpeppe/go-internal/testscript
- https://encore.dev/blog/testscript-hidden-testing-gem
