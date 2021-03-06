---
date: 2020-01-01
lastmod: ["lastmod"]
title: MailCatcher
LinkTitle: MailCatcher
description: How to test emails on your local environment.
tags: ["MailCatcher", "Ruby"]
---

# MailCatcher
* Simple SMTP server made of Ruby.
* You can check emails of web app on your local environment.

## Docker container of MailCatcher
`docker-compose.yml`
```yml
  mailcatcher:
    image: schickling/mailcatcher:latest
    ports:
      - 1080:1080
```

```shell
docker-compose up -d
```

[http://localhost:1080](http://localhost:1080)

## .env config of Laravel container
`.env.local`
```ini
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

## SMTP config of Laravel container
`/etc/ssmtp/ssmtp.conf`
```ini
root=postmaster
mailhub=mailcatcher:1025
rewriteDomain=foo-bar-baz.com
```

## References
* [Github Mail Catcher](https://github.com/sj26/mailcatcher)
* [ローカルの開発環境にSMTPを整備する](https://qiita.com/reflet/items/6e7c8d7850ff9cdb5d92)
* [Laravelローカル環境構築 メール送信の設定＋MailCatcher](https://laraweb.net/environment/8894/)