---
date: 2022-11-28
lastmod: ["lastmod"]
title: Upgrade
LinkTitle: Upgrade
description: Instant upgrades and automated refactroing
tags: ["PHP", "Rector", "Laravel", "Upgrade", "Refactoring"]
---

# PHP Upgrade

## Rector

### Overview
- [rectorphp/rector](https://github.com/rectorphp/rector)
  > Rector instantly upgrades and refactors the PHP code of your application.
- [Rules Overview](https://github.com/rectorphp/rector/blob/main/docs/rector_rules_overview.md)

### Install

```shell
$ composer require rector/rector --dev
$ composer install
$ vendor/bin/rector init
$ vi rector.php
```

```php {hl_lines=[12,17,22,29],linenostart=1}
<?php

declare(strict_types=1);

// use Rector\CodeQuality\Rector\Class_\InlineConstructorDefaultToPropertyRector;
use Rector\Config\RectorConfig;
use Rector\Set\ValueObject\LevelSetList;

return static function (RectorConfig $rectorConfig): void {
    // register paths to upgrade and refactor PHP code
    $rectorConfig->paths([
        __DIR__ . '/src',
    ]);

    // register paths to skip
    $rectorConfig->skip([
        __DIR__ . '/foo/bar',
    ]);

    // register following values
    // arguments and default values: max seconds = 120, max number of process = 16, job size = 20
    $rectorConfig->parallel(300);

    // register a single rule
    // $rectorConfig->rule(InlineConstructorDefaultToPropertyRector::class);

    // define sets of rules
    $rectorConfig->sets([
        LevelSetList::UP_TO_PHP_82,
    ]);
};

```
### Run
```shell
$ vendor/bin/rector --help
$ vendor/bin/rector process src --dry-run
$ vendor/bin/rector process src --config=rector.php
```

### Directory Structure

```shell
.
├── src # Target dir to upgrade and refactor PHP code
├── vendor
│   ├── bin
│   ├── composer
│   ├── phpstan
│   ├── rector
│   └── autoload.php
├── composer.json
├── composer.lock
└── rector.php
```

## Rector for Laravel

### Overview
- [driftingly/rector-laravel](https://github.com/driftingly/rector-laravel)
  > This package is a [Rector](](https://github.com/rectorphp/rector)) extension developed by the Laravel community.
- [Laravel Versions](https://github.com/driftingly/rector-laravel/tree/main/config/sets/level)
- [Rules Overview](https://github.com/driftingly/rector-laravel/blob/main/docs/rector_rules_overview.md)

### Install
```shell
$ composer require driftingly/rector-laravel --dev
$ composer install
$ vendor/bin/rector init
$ vi rector.php
```

```php {hl_lines=[7,30],linenostart=1}
<?php

declare(strict_types=1);

use Rector\Config\RectorConfig;
use Rector\Set\ValueObject\LevelSetList;
use RectorLaravel\Set\LaravelSetList;

return static function (RectorConfig $rectorConfig): void {
    // register paths to upgrade and refactor PHP code
    $rectorConfig->paths([
        __DIR__ . '/src',
    ]);

    // register paths to skip
    $rectorConfig->skip([
        __DIR__ . '/foo/bar',
    ]);

    // register following values
    // arguments and default values: max seconds = 120, max number of process = 16, job size = 20
    $rectorConfig->parallel(300);

    // register a single rule
    // $rectorConfig->rule(InlineConstructorDefaultToPropertyRector::class);

    // define sets of rules
    $rectorConfig->sets([
        LevelSetList::UP_TO_PHP_82,
        LaravelSetList::LARAVEL_90,
    ]);
};

```

## References
- https://github.com/rectorphp/rector
- https://github.com/driftingly/rector-laravel
- https://spatie.be/courses/front-line-php/automatically-upgrading-your-code-to-php-8-using-rector
- [PHP Conference Japan 2021: PHP8 version upgrade in legacy systems](https://www.youtube.com/watch?v=BSTcVa7dijY)