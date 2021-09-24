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

func setup() {
	// Your code
}

func shutdown() {
	// Your code
}
```