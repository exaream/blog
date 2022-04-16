---
date: 2020-01-01
lastmod: ["lastmod"]
title: Node.js
LinkTitle: Main
description: Node.js
tags: ["Node.js", "nodebrew", "Homebrew"]
---

# Node.js

## Install Homebrew
* Homebrew is the package manager for macOS.
* https://docs.brew.sh/Installation
```shell
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
$ brew -v
Homebrew 3.2.13
...
```

## Install nodebrew (using Homebrew)
* nodebrew is the package manager for Node.js.
* You can install multiple versions of Node.js and switch between them etc.

```shell
$ brew install nodebrew
$ which nodebrew
/usr/local/bin/nodebrew
$ nodebrew -v
nodebrew 1.1.0
$ nodebrew setup
...
```

## Install Node.js

### Check installable versions

```shell
$ nodebrew ls-remote
```

### Install
```shell
$ nodebrew install-binary latest
```

### Check current version
```shell {hl_lines=[4]}
$ nodebrew ls
v16.10.0

current: none
```
### Apply specific version
```shell
$ nodebrew use v16.10.0
use v16.10.0
```

### Check current version again
```shell {hl_lines=[4]}
$ nodebrew ls
v16.10.0

current: v16.10.0
```

### Create an environment path

```shell
$ echo 'export PATH=$HOME/.nodebrew/current/bin:$PATH' >> ~/.bash_profile
$ source ~/.bash_profile
```

### Check if we use ```node```

```shell
$ node -v
v16.10.0
```

