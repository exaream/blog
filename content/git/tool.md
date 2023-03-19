---
date: 2020-01-01
lastmod: ["lastmod"]
title: Git Tool
linkTitle: Tool
description: Git Tool
tags: ["Git","git-split-diffs","git-secrets", "Tool"]
---

# Git Tool

## git-secrets

- `git-secrets` scans a git repository, finds credential information and prevents them from being committed.

### References
- https://github.com/awslabs/git-secrets
- https://blog.katsubemakito.net/git/git-secrets

### Install
macOS
```shell
$ brew install git-secrets
```

```shell
$ git clone https://github.com/awslabs/git-secrets.git
$ cd git-secrets
$ make install
```

### Basic usage
Set git-secrets to a git repository
```shell
$ git init
$ git secrets --install
```
Check commit history
```shell
$ git secrets --scan-history
```
For AWS
```shell
$ git secrets --register-aws
```

## git-split-diffs
- Display the results of the `git diff` command side by side like GitHub.

### References
- https://genzouw.com/entry/2021/05/24/104932/2640/

### Precondition
- `npm` or `yarn`

### Install
If you use `npm`
```shell
$ npm install -g git-split-diffs
```
or, if you use `yarn`
```shell
$ yarn global add git-split-diffs
```

### Add configuration of git-split-diffs to .gitconfig
```shell
$ git config --global core.pager "git-split-diffs --color | less -RFX"
```

### Check
```shell
$ git log -p
```
```shell
$ git diff <branch A> <branch B>
```

### Additional info
If you want to use light theme
```shell
$ git config --global split-diffs.theme-name light
```

If you want to use dark theme
```shell
$ git config --global split-diffs.theme-name dark
```

Adjust the minimum display width if you want to display the difference side by side even if the terminal display is narrow.
```shell
$ git config --global split-diffs.min-line-width 40
```
