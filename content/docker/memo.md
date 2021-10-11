---
date: 2020-01-01
lastmod: ["lastmod"]
title: Docker Memo
tags: ["Docker", "Container"]
---

# Docker Memo

### コンテナに割り振られたIPアドレスを取得
```shell
$ docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <CONTAINER ID>
```

### コンテナに割り振られたMACアドレスを取得
```shell
$ docker inspect --format='{{range .NetworkSettings.Networks}}{{.MacAddress}}{{end}}' <CONTAINER ID>
```

## Stop

### Stop all containers

```shell
docker container ps -q | xargs docker container stop
```
or
```shell
docker stop $(docker ps -q)
```

## Remove

### Stop and remove all (containers, images, volumes, networks) 滅びの呪文

```shell
docker-compose down --rmi all --volumes --remove-orphans
```

### Remove stopped containers, untagged images, unused volumes, unused networks

```shell
docker system prune
```

|option|description|
|---|---|
|-a|未使用のイメージを含め削除|
|-f|確認せず削除|

```shell
docker system prune -af
```

### Remove stopped containers
```shell
docker container prune
```

### Remove all containers
```shell
docker container ps -aq | xargs docker container rm -f
```
or
```shell
docker ps -aq | xargs docker rm -f
```
or
```shell
docker rm -f $(docker ps -aq)
```

### Remove all images (after all containers were removed)

```shell
docker images -aq | xargs docker image rm -f
```
or
```shell
docker images -aq | xargs docker rmi -f
```
or
```shell
docker rmi -f $(docker images -aq)
```

### Remove untagged images

```shell
docker images -fq "dangling=true" | xargs docker image rm
```

### Remove unused volumes

```shell
docker volume prune
```

### Remove unused networks

```shell
docker network prune
```

## Log

### How to check log

最新の30件を確認
```shell
docker logs -f --tail=30
```

## Other
### How to mount a directory in host server
```shell
docker run -v [absolute path in host]:[absolute path in container] [image name]:[tag] [command]
```

## References
* https://qiita.com/saki-engineering/items/5791c609fcf055e13bca
* https://qiita.com/gold-kou/items/44860fbda1a34a001fc1