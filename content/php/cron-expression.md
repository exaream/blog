---
date: 2020-01-01
lastmod: ["lastmod"]
title: PHP Cron Expression
LinkTitle: Cron Expression
description: PHP Cron Expression
tags: ["PHP", "Cron Expression"]
---

# PHP Cron Expression

## Install
```shell
composer self-update --2
composer require dragonmantank/cron-expression
composer update
```

## Example
```php
<?php

require_once 'vendor/autoload.php';

$schedule = '10 2 * * *';

$cron = new Cron\CronExpression($schedule);
$dates = $cron->getMultipleRunDates(3);
foreach ($dates as $date) {
    echo $date->format('Y-m-d H:i:s') . PHP_EOL;
}
```
