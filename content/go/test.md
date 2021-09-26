---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Test
description: "H"
tags: ["Go", "Golang", "Test"]
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
```shell
$ go test ./... -cover
```

### Clean test cache
If you see `(cached)` in results of unit tests
```bash
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

