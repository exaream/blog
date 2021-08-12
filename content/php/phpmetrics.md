---
date: 2020-01-01
lastmod: ["lastmod"]
title: PhpMetrics
tags: ["PhpMetrics","Metrics"]
---

# PhpMetrics

## PhpMetrics とは
* PHPプログラムの静的解析ツール
* 結果レポートをHTMLで出力
* 期待する効果
  * 可読性・メンテナンス性・再利用性・テスト容易性の向上
  * バグ混入率の低下

### 解析時の評価指標
* 複雑度(循環的複雑度、経路複雑度)
* オブジェク指向(凝集度、結合度、抽象化)
* コード量 etc.

## インストール

```bash
$ composer require phpmetrics/phpmetrics --dev
```

## 使用方法
```bash
$ php ./vendor/bin/phpmetrics --report-html=myreport.html /path/to/your/sources/
```

## 参考
* https://phpmetrics.org/
* https://github.com/phpmetrics/PhpMetrics
