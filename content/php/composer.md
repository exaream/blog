---
date: 2020-11-01
lastmod: ["lastmod"]
title: Composer
LinkTitle: Composer
description: How to use a dependency manager for PHP
tags: ["PHP", "Composer", "Dependency Manager"]
draft: true
---

# Composer

## 概要
- PHPのパッケージ管理ツール
  - パッケージの依存関係を管理するツール

### macOS

#### Normal upgrade
```shell
$ brew update
$ brew upgrade php
$ php -v
```

#### Upgrade with `shivammathur/homebrew-php`
```shell
$ brew update
$ brew tap shivammathur/php
$ brew install shivammathur/php/php@8.3
$ brew link --overwrite --force php@8.3
$ php -v
```

## How to install Composer

- https://getcomposer.org/download/
```shell
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
$ php -r "if (hash_file('sha384', 'composer-setup.php') === '55ce33d7678c5a611085589f1f3ddf8b3c52d662cd01d4ba75c0ee0459970c2200a51f492d557530c71c15d8dba01eae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
$ php composer-setup.php
$ php -r "unlink('composer-setup.php');"
$ sudo mv composer.phar /usr/local/bin/composer
```

## How to write Composer configration

Directory Structure
```text
<package>/
    .git/
    .gitignore
    composer.json
    src/
        <namspace>/
            <class>.php
    tests/
        <namspace>/
            <class>Test.php
```

`<package>/composer.json`
```json
{
    "name": "<github user name>/<package name>",
    "description": "Sample for loading other repositories using Composer",
    "type": "library",
    "authors": [
        {
            "name": "Developer Name",
            "email": "sample@sample.com"
        }
    ],
    "require": {
        "php": "^8.3"
    },
    "autoload": {
        "psr-4": {"<NamespacePrefix>\\": "src/"}
    }
}
```

## How to write Laravel side
- Reference: https://www.it-swarm.dev/ja/composer-php/822510661/
```json
{
    "require": {
        ...
        "exaream/sample": "dev-master"
    },
    ...
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/exaream/sample.git"
        }
    ],
    ...
}
```

特定のGitタグを指定する場合
```json
"require": {
    "exaream/sample": "dev-master#v1.0.0"
},
```
最新のGitコミットを指定する場合
```json
"require": {
    "exaream/sample": "dev-master"
},
```
特定のGitコミットを指定する場合
```json
"require": {
    "exaream/sample": "dev-master#1ba4d336"
},
```