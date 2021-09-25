---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Test
description: "H"
tags: ["Go", "Golang", "Test"]
---

# Go Test

## Unit Test

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
```go {hl_lines=["14-15"],linenostart=1}
func TestSample(t *testing.T) {
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

