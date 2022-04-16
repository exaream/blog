---
date: 2020-01-01
lastmod: ["lastmod"]
title: Microservices Architecture
LinkTitle: Architecture
description: Microservices Architecture
#tags: ["Microservices", "Architecture"]
draft: true
---

# Microservices Architecture

Microservices Patterns
* https://microservices.io/patterns/microservices.html 英語のページ
* https://github.com/microservices-patterns/ftgo-application 実装サンプル（Java）
* https://books.rakuten.co.jp/rb/16239274/ 紙
* https://book.impress.co.jp/books/1118101063 紙 or 電子版

```
サービス境界を超えるオブジェクトの参照を削減
サービス境界を超えるデータ整合を維持するためにSagaパターンを使用
1つのアグリゲートとは、複数のオブジェクトをまとめあげた1つのクラスタ（1つのユニット）
他のアグリゲートを参照する際は主キーを使用


Transaction scriptパターン
リクエストごとに1つづつ手続き型のトランザクションスクリプトを下記、トランザクションスクリプトのコレうションとしてビジネスモデルを構成する
動作を実装するクラス
　FooServiceクラス：リクエスト/システム操作ごとに1メソッド
　FooDao(data access object)クラス：動作はほとんど含まれない
状態を実装するクラス


Domain model パターン
状態と動作をあわせ持つクラスによって構成されるオブジェクトモデルとビジネスロジックを構成する
　FooService
1つの大きなクラスではなく、それぞれ少数の仕事を分担する複数の小さなクラスによって設計
P172 （ドメインと呼んでいるのは）アグリゲート。アグリゲート削除するとアグリゲートに含まれる全てのオブジェクトが当該DBから削除される。
並行処理ではアグリゲートルートをロック。クライアントはアグリゲートルートのメソッドを呼び出さなければいけない。
DDDでドメインモデルを設計する際の重要なポイントは、アグリゲートとその境界、ルートを明らかにすること。

アグリゲートはトランザクションの範囲であるため
粒度はできるだけ小さいほうが良い
P177　アグリゲートを使ったビジネスロジックの設計例

ルール
アグリゲートの外部からはアグリゲートルートだけを参照する
アグリゲート間の参照では主キー（オブジェクト参照ではなく）ID）のみを使用しなければならない
P175　1つのトランザクションで1つのアグリゲートを作成または更新

P165
inbound adapter クライアントからのリクエストを処理しビジネスロジックを呼び出す
outbound adapter ビジネスロジックから呼び出され、他のサービスやアプリケーションを呼び出す

P169
エンティティ、ファクトリ、リポジトリ、サービスというクラスの説明

ドメインイベント
●どのようなことでドメインイベントが使用されるか
CQRS（Command Query Responsibility Segregation）
レプリカを持っているサービスに、レプリカのソースデータが変わったことを通知する
ビジネスプロセスの次のステップに進むためにWebhookやメッセージブローカーを介して他のアプリケーションに通知する
Elasticsearchを更新したりWebブラウザに通知する等、同じアプリケーションの別のコンポーネントに通知する
ユーザーへの通知
監視しアプリケーションの正常動作を確認

●P179 実際のクラスの例
●イベントのコンシューマーが必要とする情報をイベント内に持たせる

メッセージブローカー：メッセージの仲介者、Apache Kafkaなど　https://hogetech.info/oss/apache/kafka


サーガを呼び出す場合は、ステータスに承認保留、キャンセル保留などの保留状態を用意し、サーガから承認やキャンセルが返却された場合は承認済・キャンセル済に更新
サーガから成功が返却されなかった場合は、前の状態に戻す

Outbox Pattern のイベント発行
select * from events where published = 0 order by id asc — イベントを検索
メッセージブローカーにイベントをパブリッシュ
update events set published = 1 where id in(xxx, xxx) — イベント発行済に更新

DBのトランザクション・ログをテーリングする方法もあり MySQL の binlog 等
今回、インド側のPoCは MySQL のTriggerを用いて Logstash で elasticsearch に同期
だけど、この方法だとアカウントオープンのような大量イベントがある場合、パフォーマンスが下がる可能性がある

outbox のテーブルはRedis（NoSQL）ではなくMySQL（RDB）を使用する
アトミックにトランザクション管理ができないため
outbox テーブルはバッチで定期的に古いレコードを削除（当初は運用を考慮し1ヶ月分くらいを保持したほうが良いかもしれない

イベント
event id でサーガを作ることで一意性を担保
冪等性のキーを用いることで重複するアグリゲート要求を破棄できるようにする
P247 図6.13 重複する要求を送信しない仕組み
サーガオーケストラはコマンドのイベント（サーガオーケストラへの一意のリプライIDを含む）をストアに保管し永続化
ストアからイベントを取り出し精査した上でメッセージブローカに送信
リプライが帰ってきたらサーガオーケストラはリプライIDを元に重複を破棄
```