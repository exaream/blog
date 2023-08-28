---
date: 2022-03-31
lastmod: ["lastmod"]
title: How to downgrade Go version on Mac
linkTitle: Downgrade Version
description: Go Check List
tags: ["Go", "Golang", "Check List"]
---

# Downgrade Go version on Mac

```shell
% brew upgrade
% go version
go version go1.21.0 darwin/arm64
% brew unlink go
% brew search go@1.18
% brew install go@1.18
% brew link --force go@1.18
% echo 'export PATH="/opt/homebrew/opt/go@1.18/bin:$PATH"' >> ~/.zshrc
% source ~/.zshrc
% go version
go version go1.18.10 darwin/arm64
```