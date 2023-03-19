---
date: 2023-03-19
lastmod: ["lastmod"]
title: ghq
linkTitle: ghq
description: ghq is a command-line tool for managing local clones of Git repositories.
tags: ["ghq", "GitHub"]
---

# `ghq`

`ghq` is a command-line tool for managing local clones of Git repositories.

### Install `ghq`

```shell
% brew install ghq
```
### Configure

```shell
% ghq root /path/to/your/dir/
% git config --global ghq.root /path/to/your/dir/
```

### How to use `ghq` command

List repositories
```shell
ghq list
```
Clone a repository
```shell
ghq get github.com/foo/bar
```

Delete a repository
```shell
ghq delete github.com/foo/bar
```
