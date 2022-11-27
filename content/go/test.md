---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Test
linkTitle: Test
description: Go Test
tags: ["Go", "Golang", "Test", "Testing"]
---

# Go Test

## Unit Test

### Run
#### Run all unit tests
```shell
$ go test ./...
```
Show detailed information
```shell
$ go test ./... -v
```

Disable test cache
```shell
$ go test ./... -count=1
```

#### Run a method of a unit test
```shell
$ go test ./mypkg/ -run TestSample
```

### Coverage

Output coverage to a terminal
```shell
$ go test -cover ./...
```

Output the coverage profile to a file and view it in a browser
```shell
$ go test -coverprofile=cover.out ./...
$ go tool cover -html=cover.out
```

Output the coverage profile to a file and convert it to a html file
```shell
$ go test -coverprofile=cover.out ./...
$ o tool cover -html=cover.out -o cover.html
```

### Clean test cache
If you see `(cached)` in results of unit tests
```shell
$ go clean -testcache
```

### Setup and shutdown
```go
func TestMain(m *testing.M) {
	setup()
	code := m.Run()
	shutdown()
	os.Exit(code)
}

func setup() { // Voluntary method name
	// Your code
}

func shutdown() { // Voluntary method name
	// Your code
}
```

### Run subtests in parallel
```go {hl_lines=[2,14,15],linenostart=1}
func TestSample(t *testing.T) {
	t.Parallel() // Run main test in parallel
	t.Logf("Start: %s", time.Now())
	tests := []struct {
		name string
		code string
		msg  string
	}{
		{"1", "0001", "foo"},
		{"2", "0002", "bar"},
		{"3", "0003", "baz"},
	}
	for _, test := range tests {
		t.Run(test.name, func(t *testing.T) { // Run subtests
			t.Parallel() // Run subtests in parallel
			if msg := mypkg.Sample(test.code); msg != test.msg {
				t.Errorf("The message must be %s: %s", test.msg, msg)
			}
		})
	}

	t.Logf("End: %s", time.Now())
}
```

## References
- [非公開（unexported）な機能を使ったテスト](https://engineering.mercari.com/blog/entry/2018-08-08-080000/)
- [go-cmpを使う理由とTipsの紹介](https://tutuz-tech.hatenablog.com/entry/2020/12/07/220236)
- [クロージャ内では(*testing.T).Fatalメソッドの呼び出しを避ける理由](https://zenn.dev/tenntenn/articles/cadd13074e6d61)