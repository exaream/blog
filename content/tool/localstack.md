---
date: 2023-08-29
lastmod: ["lastmod"]
title: localstack
LinkTitle: Localstack
description: localstack
tags: ["localstack", "AWS"]
---

# localstack on Mac

## Install

https://docs.localstack.cloud/getting-started/installation/

```shell
% brew install localstack/tap/localstack-cli

% localstack --version
2.3.2

% localstack start -d

     __                     _______ __             __
    / /   ____  _________ _/ / ___// /_____ ______/ /__
   / /   / __ \/ ___/ __ `/ /\__ \/ __/ __ `/ ___/ //_/
  / /___/ /_/ / /__/ /_/ / /___/ / /_/ /_/ / /__/ ,<
 /_____/\____/\___/\__,_/_//____/\__/\__,_/\___/_/|_|

 💻 LocalStack CLI 2.3.2

[10:57:13] starting LocalStack in Docker mode 🐳                localstack.py:495
           preparing environment                                bootstrap.py:1206
[10:57:14] configuring container                                bootstrap.py:1214
[10:57:19] starting container                                   bootstrap.py:1224
[10:58:15] detaching                                            bootstrap.py:122

% localstack status services
┏━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━┓
┃ Service                  ┃ Status      ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━┩
│ acm                      │ ✔ available │
│ apigateway               │ ✔ available │
│ cloudformation           │ ✔ available │
│ cloudwatch               │ ✔ available │
│ config                   │ ✔ available │
│ dynamodb                 │ ✔ available │
│ dynamodbstreams          │ ✔ available │
│ ec2                      │ ✔ available │
│ es                       │ ✔ available │
│ events                   │ ✔ available │
│ firehose                 │ ✔ available │
│ iam                      │ ✔ available │
│ kinesis                  │ ✔ available │
│ kms                      │ ✔ available │
│ lambda                   │ ✔ available │
│ logs                     │ ✔ available │
│ opensearch               │ ✔ available │
│ ram                      │ ✔ available │
│ redshift                 │ ✔ available │
│ resource-groups          │ ✔ available │
│ resourcegroupstaggingapi │ ✔ available │
│ route53                  │ ✔ available │
│ route53resolver          │ ✔ available │
│ s3                       │ ✔ available │
│ s3control                │ ✔ available │
│ scheduler                │ ✔ available │
│ secretsmanager           │ ✔ available │
│ ses                      │ ✔ available │
│ sns                      │ ✔ available │
│ sqs                      │ ✔ available │
│ ssm                      │ ✔ available │
│ stepfunctions            │ ✔ available │
│ sts                      │ ✔ available │
│ support                  │ ✔ available │
│ swf                      │ ✔ available │
│ transcribe               │ ✔ available │
└──────────────────────────┴─────────────┘

% brew install awscli

% which aws
/usr/local/bin/aws

% aws --version
aws-cli/2.13.32 Python/3.11.6 Darwin/21.6.0 source/x86_64 prompt/off

% aws configure --profile localstack
AWS Access Key ID [None]: dummy
AWS Secret Access Key [None]: dummy
Default region name [None]: us-east-1
Default output format [None]: json

% cat ~/.aws/credentials
[localstack]
aws_access_key_id = dummy
aws_secret_access_key = dummy

% cat ~/.aws/config
[profile localstack]
region = us-east-1
output = json
```


## SQS (Simple Queue Service)

- 概要
  - システムからメッセージを受け取り、一時的に保存した上で処理
  - 大量のメッセージを平準化して(均等な作業量で)処理するためのバッファ(緩衝材)として  
    または、Microservice間のタスクの非同期処理として使用
  - FIFO (First-In-First-Out)キューを使用するとメッセージの順序や一貫性を保証

SQSキューの作成
```shell
% aws sqs create-queue --queue-name test-queue --endpoint-url http://localhost:4566 --profile localstack 
```

SQSキューの一覧
```shell
% aws sqs list-queues --endpoint-url http://localhost:4566 --profile localstack
{
    "QueueUrl": "http://localhost:4566/000000000000/test-queue"
}
```

SQSキューの属性
```shell
SQSキューの属性
% aws sqs get-queue-attributes --queue-url http://localhost:4566/000000000000/test-queue --attribute-names All --endpoint-url http://localhost:4566 --profile localstack

{
    "Attributes": {
        "ApproximateNumberOfMessages": "0",
        "ApproximateNumberOfMessagesNotVisible": "0",
        "ApproximateNumberOfMessagesDelayed": "0",
        "CreatedTimestamp": "1693055519",
        "DelaySeconds": "0",
        "LastModifiedTimestamp": "1693055519",
        "MaximumMessageSize": "262144",
        "MessageRetentionPeriod": "345600",
        "QueueArn": "arn:aws:sqs:us-east-1:000000000000:test-queue",
        "ReceiveMessageWaitTimeSeconds": "0",
        "VisibilityTimeout": "30",
        "SqsManagedSseEnabled": "false"
    }
}
```

SQSキューへメッセージを送信
```shell
%  aws sqs send-message --queue-url "http://localhost:4566/000000000000/test-queue" --message-body "hello sqs" --endpoint-url http://localhost:4566 --profile localstack

{
    "MD5OfMessageBody": "3b7bef57d06c0021d0aafe8f6d587241",
    "MessageId": "24b091c2-63fa-4814-bb84-30ff1930f5fd"
}
```

SQSキューの現在のメッセージ数を確認
```shell
% aws sqs get-queue-attributes --queue-url 'http://localhost:4566/000000000000/test-queue' --attribute-names ApproximateNumberOfMessages --query 'Attributes.ApproximateNumberOfMessages' --endpoint-url http://localhost:4566 --profile localstack

"1"
```

SQSキューの消費
```shell
% aws sqs receive-message --queue-url 'http://localhost:4566/000000000000/test-queue' --endpoint-url http://localhost:4566 --profile localstack

{
    "Messages": [
        {
            "MessageId": "24b091c2-63fa-4814-bb84-30ff1930f5fd",
            "ReceiptHandle": "YzNjMDIzZTEtNTBkZC00NmE5LTg3NmItYTYxOTI3YzE3MWIzIGFybjphd3M6c3FzOnVzLWVhc3QtMTowMDAwMDAwMDAwMDA6dGVzdC1xdWV1ZSAyNGIwOTFjMi02M2ZhLTQ4MTQtYmI4NC0zMGZmMTkzMGY1ZmQgMTY5MzA1NTk5MC42ODAyMTQ2",
            "MD5OfBody": "3b7bef57d06c0021d0aafe8f6d587241",
            "Body": "hello sqs"
        }
    ]
}
```

SQSキューのメッセージを削除
```shell
% aws sqs delete-message --queue-url 'http://localhost:4566/000000000000/test-queue' --receipt-handle "YzNjMDIzZTEtNTBkZC00NmE5LTg3NmItYTYxOTI3YzE3MWIzIGFybjphd3M6c3FzOnVzLWVhc3QtMTowMDAwMDAwMDAwMDA6dGVzdC1xdWV1ZSAyNGIwOTFjMi02M2ZhLTQ4MTQtYmI4NC0zMGZmMTkzMGY1ZmQgMTY5MzA1NTk5MC42ODAyMTQ2" --endpoint-url http://localhost:4566 --profile localstack
```

SQSキューのメッセージ数を確認
```shell
% aws sqs get-queue-attributes --queue-url 'http://localhost:4566/000000000000/test-queue' --attribute-names ApproximateNumberOfMessages --query 'Attributes.ApproximateNumberOfMessages' --endpoint-url http://localhost:4566 --profile localstack

"0"
```

## SNS

- 概要
  - 通知の発行者からメッセージを受け取り、購読者(サブスクライバー)に配信
  - 複数のエンドポイント(SQSキュー、Lambda関数、メール、SMS、HTTP/HTTPS etc)に送信可
  - システム間の非同期のメッセージの交換、イベント駆動型のワークフロー(データの登録・変更等の  
    特定のイベントが発生した際、メッセージを送信し、受け取ったシステムが処理を実行する等)を実現

SNSトピックの作成
```shell
% aws sns create-topic --name test-topic --endpoint-url=http://localhost:4566 --profile localstack
{
    "TopicArn": "arn:aws:sns:us-east-1:000000000000:test-topic"
}
```

SQSキューの作成
```shell
% aws sqs create-queue --queue-name test-queue --endpoint-url=http://localhost:4566 --profile localstack
{
    "QueueUrl": "http://localhost:4566/000000000000/test-queue"
}
```

SNSトピックをSQSキューで購読し、SNSトピックに対してメッセージが発行されたら SQSに自動送信されるよう設定
```shell
% aws sns subscribe --topic-arn arn:aws:sns:us-east-1:000000000000:test-topic --protocol sqs --notification-endpoint arn:aws:sqs:us-east-1:000000000000:test-queue --endpoint-url=http://localhost:4566 --profile localstack
{
    "SubscriptionArn": "arn:aws:sns:us-east-1:000000000000:test-topic:7284befa-eb97-4f8e-a76e-63891be75bd3"
}
```

SNSトピックへのメッセージを発行
```shell
% aws sns publish --topic-arn arn:aws:sns:us-east-1:000000000000:test-topic --message 'Hello, world!' --endpoint-url http://localhost:4566 --profile localstack
{
    "MessageId": "63af4d31-59c3-4222-bc73-1ad0724592f4"
}
```

SNSトピックを購読しているSQSキューからメッセージを受信できることを確認
(ここでは上記のMessageId `63af4d31-59c3-4222-bc73-1ad0724592f4` が `Body` に含まれていることを確認)
```shell
% aws sqs receive-message --queue-url http://localhost:4566/000000000000/test-queue --endpoint-url http://localhost:4566 --profile localstack
{
    "Messages": [
        {
            "MessageId": "10ba045a-6c00-44b2-b3df-6774eacd97a2",
            "ReceiptHandle": "MjM5OTk2ZjctMzJhNC00OTRkLWJjZTYtMDgyMzEwYTA5M2EwIGFybjphd3M6c3FzOnVzLWVhc3QtMTowMDAwMDAwMDAwMDA6dGVzdC1xdWV1ZSAxMGJhMDQ1YS02YzAwLTQ0YjItYjNkZi02Nzc0ZWFjZDk3YTIgMTY5OTA2NDYyOS42ODU2NzQ=",
            "MD5OfBody": "095875699e9fdb2ea41cf6385f0ea1e9",
            "Body": "{\"Type\": \"Notification\", \"MessageId\": \"63af4d31-59c3-4222-bc73-1ad0724592f4\", \"TopicArn\": \"arn:aws:sns:us-east-1:000000000000:test-topic\", \"Message\": \"Hello, world!\", \"Timestamp\": \"2023-11-04T02:20:13.239Z\", \"SignatureVersion\": \"1\", \"Signature\": \"EXAMPLEpH+..\", \"SigningCertURL\": \"https://sns.us-east-1.amazonaws.com/SimpleNotificationService-0000000000000000000000.pem\", \"UnsubscribeURL\": \"http://localhost:4566/?Action=Unsubscribe&SubscriptionArn=arn:aws:sns:us-east-1:000000000000:test-topic:7284befa-eb97-4f8e-a76e-63891be75bd3\"}"
        }
    ]
}
```

## References
- https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-queue-message-identifiers.html
- https://qiita.com/KWS_0901/items/bb0d3c8319cbb1e64217
