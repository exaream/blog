---
date: 2021-07-31
lastmod: ["lastmod"]
title: Go Memo
description: "H"
tags: ["Go", "Golang", "Memo"]
---

# Go Memo

## File

### 拡張子を除外したファイル名を取得
```go
filepath.Base(path[:len(path)-len(filepath.Ext(path))])
```

## Error

### エラーチェック漏れを確認

```bash
go get github.com/kisielk/errcheck
```

```bash
errcheck ./...
```