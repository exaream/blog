---
date: 2020-01-01
lastmod: ["lastmod"]
title: Docker Memo
tags: ["Docker", "Container"]
---

# Docker Memo

## Stop

### Stop all containers

```bash
docker container ps -q | xargs docker container stop
```
or
```bash
docker stop $(docker ps -q)
```

## Remove

### Stop and remove all (containers, images, volumes, networks) 滅びの呪文

```bash
docker-compose down --rmi all --volumes --remove-orphans
```

### Remove stopped containers, untagged images, unused volumes, unused networks

```bash
docker system prune
```

|option|description|
|---|---|
|-a|未使用のイメージを含め削除|
|-f|確認せず削除|

```bash
docker system prune -af
```

### Remove stopped containers
```bash
docker container prune
```

### Remove all containers
```bash
docker container ps -aq | xargs docker container rm -f
```
or
```bash
docker ps -aq | xargs docker rm -f
```
or
```bash
docker rm -f $(docker ps -aq)
```

### Remove all images (after all containers were removed)

```bash
docker images -aq | xargs docker image rm -f
```
or
```bash
docker images -aq | xargs docker rmi -f
```
or
```bash
docker rmi -f $(docker images -aq)
```

### Remove untagged images

```bash
docker images -fq "dangling=true" | xargs docker image rm
```

### Remove unused volumes

```bash
docker volume prune
```

### Remove unused networks

```bash
docker network prune
```

## Log

### How to check log

最新の30件を確認
```bash
docker logs -f --tail=30
```

## Other
### How to mount a directory in host server
```bash
docker run -v [absolute path in host]:[absolute path in container] [image name]:[tag] [command]
```

## References
* https://qiita.com/gold-kou/items/44860fbda1a34a001fc1