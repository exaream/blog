---
date: 2022-08-10
lastmod: ["lastmod"]
title: Homebrew
linkTitle: Homebrew
description: Homebrew
tags: ["Homebrew", "brew", "Mac", "macOS"]
---

## Overview
[Homebrew](https://brew.sh/) is a package maneger for macOS.

## Install
```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Command

Show help.
```shell
brew help
```

Update Homebrew itself.
```shell
brew update
```

Update packages.
```shell
brew upgrade
```

Install a package.
```shell
brew install <package name>
```
e.g.
```shell
brew install wget
```

Search a package.
```shell
brew search <package name>
```
```shell
brew search /regex/
```

List all installed packages.
```shell
brew list
```

List all installed packages with parent directories
```shell
brew list -v
```

Uninstall an package.
```shell
brew uninstall <package name>
```

List installed packages that have an updated version available.
```shell
brew outdated
```

Remove outdated packages.
```shell
brew cleanup
```
Confirm outdated packages before removing.
```shell
brew cleanup --dry-run
```

