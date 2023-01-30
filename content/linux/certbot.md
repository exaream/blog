---
date: 2023-01-30
lastmod: ["lastmod"]
title: Certbot
linkTitle: Certbot
description: Certbot
#tags: ["Linux","Certbot"]
draft: true
---

# Certbot
- Let's Encrypt

```shell
$ git clone https://github.com/certbot/certbot.git
$ cd certbot
```

```shell
$ ./certbot-auto certonly --webroot --webroot-path /var/www/html/sample.com/webroot/ \
   --domain sample.com \
   --email sample@gmail.com \
   --agree-tos -n
```
or
```shell
$ ./certbot-auto certonly --webroot -w /var/www/html/sample.com/webroot/ \
   -d sample.com \
   -m sample@gmail.com \
   --agree-tos -n
```

```shell
server {
    listen 443 ssl;
    ssl_certificate      /etc/letsencrypt/live/ample.com/fullchain.pem;
    ssl_certificate_key  /etc/letsencrypt/live/ample.com/privkey.pem;
    server_name          sample.com;
```

```shell
0 3 1 * * /usr/local/lib/certbot/certbot-auto renew --post-hook 'sudo service nginx reload'
```

## References
- https://songmu.jp/riji/entry/2019-06-10-certbot-https.html
