---
date: 2020-01-01
lastmod: ["lastmod"]
title: MailCatcher
LinkTitle: MailCatcher
description: How to test emails on your local environment.
tags: ["MailCatcher", "Ruby"]
---

# MailCatcher

## What is MailCatcher?
- Simple SMTP server made of Ruby.
- You can check emails of Web app on your local environment.

## Overview
- This post only explains how to set up MailCatcher container using Docker Compose to check emails sent from a Web app like Laravel.
- NOTE: This post describes only MailCatcher. Please add what you need.
```text
# Directory structure of sample
project-dir
├── .git
├── .gitignore
├── docker-compose.yml
├── service
│   ├── Dockerfile
│   ├── laravel
│   │   ├── .env
│   │   ├── app
│   │   └── ...
│   └── setup
│       ├── etc
│       │   ├── ssmtp
│       │   │   └── ssmtp.conf ★
│       │   └── ...
│       ├── scripts
│       │   ├── run.sh ★
│       │   └── ...
```

## Configuration

### Docker Compose

`docker-compose.yml`
```yml
  # NOTE: This post describes only MailCatcher. Please add what you need.
  mailcatcher:
    image: schickling/mailcatcher:latest
    ports:
      - 1080:1080
    networks:
      - container-link

  networks:
    # Prevent generating "default" network automatically.
    default:
      external: true
      name: bridge
    # Network segment of inter-container communication.
    container-link:
      name: sample_network
```

### Docker container for Web app

`Dockerfile`
```dockerfile
# NOTE: This post describes only MailCatcher. Please add what you need.

RUN apk --update --no-cache add \
    ssmtp

RUN mkdir -p /scripts \
    && chmod -R 755 /scripts
    && chown -R foo:bar /etc/ssmtp/

ADD setup/scripts/run.sh /scripts/run.sh
ADD setup/etc/ssmtp/ssmtp.conf /etc/ssmtp/ssmtp.conf

ENTRYPOINT ["/scripts/run.sh"]
```

`.env`
```ini
APP_NAME="Sample App"

MAIL_DRIVER=smtp
MAIL_HOST=mailcatcher
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

# Optional configuration
MAIL_FROM_ADDRESS=mailcatcher@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

`/etc/ssmtp/ssmtp.conf`
```ini
root=postmaster
mailhub=mailcatcher:1025
rewriteDomain=foo-bar-baz.com
```



## Run

```shell
docker compose up -d
```
[http://localhost:1080](http://localhost:1080)


## References
* [Github Mail Catcher](https://github.com/sj26/mailcatcher)
* [ローカルの開発環境にSMTPを整備する](https://qiita.com/reflet/items/6e7c8d7850ff9cdb5d92)
* [Laravelローカル環境構築 メール送信の設定＋MailCatcher](https://laraweb.net/environment/8894/)