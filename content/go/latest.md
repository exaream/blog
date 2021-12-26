---
date: 2021-07-31
lastmod: ["lastmod"]
title: Golang latest version
description: ""
tags: ["Go", "Golang", "latest version"]
---

# Golang latest version

## Install go1.18beta1
https://go.dev/doc/tutorial/generics

```shell {hl_lines=[3,4,6]}
$ go version
go version go1.17.2 darwin/amd64
$ go install golang.org/dl/go1.18beta1@latest
$ go1.18beta1 download
$ vi ~/.bash_profile
alias go=go1.18beta1
$ source ~/.bash_profile
$ go version
go version go1.18beta1 darwin/amd64
```

## Run the following
```shell
$ go mod init sample
$ go get golang.org/x/exp
```
https://gotipplay.golang.org/p/ifloU6kyjMt
```go
package main

import (
	"fmt"

	"golang.org/x/exp/maps"
	"golang.org/x/exp/slices"
)

func main() {
	colors := map[string]string{
		"White": "#FFFFFF",
		"Black": "#000000",
		"Blue":  "#0000FF",
		"Red":   "#FF0000",
		"Green": "#00FF00",
	}
	fmt.Println(maps.Keys(colors))
	fmt.Println(maps.Values(colors))

	fmt.Println(slices.Contains(maps.Keys(colors), "White"))
}
```

```shell
$ go run main.go
```