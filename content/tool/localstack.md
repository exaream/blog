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
```shell
% brew install localstack/tap/localstack-cli

% localstack start -d

     __                     _______ __             __
    / /   ____  _________ _/ / ___// /_____ ______/ /__
   / /   / __ \/ ___/ __ `/ /\__ \/ __/ __ `/ ___/ //_/
  / /___/ /_/ / /__/ /_/ / /___/ / /_/ /_/ / /__/ ,<
 /_____/\____/\___/\__,_/_//____/\__/\__,_/\___/_/|_|

 💻 LocalStack CLI 2.2.0

[22:13:25] starting LocalStack in Docker mode 🐳                                                       localstack.py:409
           preparing environment                                                                        bootstrap.py:623
LocalStack container named "localstack_main" is already running


% localstack status services
┏━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━┓
┃ Service                  ┃ Status      ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━┩
│ acm                      │ ✔ available │
│ apigateway               │ ✔ available │
│ cloudformation           │ ✔ available │
│ cloudwatch               │ ✔ running   │
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
│ sqs                      │ ✔ running   │
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
aws-cli/2.13.13 Python/3.11.5 Darwin/21.6.0 source/x86_64 prompt/off

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
output = text
```

## Handle queue

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

## References
- https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-queue-message-identifiers.html
- https://qiita.com/KWS_0901/items/bb0d3c8319cbb1e64217
