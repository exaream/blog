---
date: 2020-01-01
lastmod: ["lastmod"]
title: Docker Memo
tags: ["Docker", "Container"]
---

# Docker Memo

## Inspection

### Get instance's information
```shell
$ docker inspect <CONTAINER ID|NAME>
```
### Get an instance’s IP address
```shell
$ docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <CONTAINER ID|NAME>
```

### Get an instance’s MAC address
```shell
$ docker inspect --format='{{range .NetworkSettings.Networks}}{{.MacAddress}}{{end}}' <CONTAINER ID|NAME>
```

### Get an instance’s log path
```shell
$ docker inspect --format='{{.LogPath}}' <CONTAINER ID|NAME>
```

### Get an instance’s image name
```shell
$ docker inspect --format='{{.Container.Spec.Image}}' <CONTAINER ID|NAME>
```

### List all port bindings
```shell
$ docker inspect --format='{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <CONTAINER ID|NAME>
```

### Find a specific port mapping
```shell
$ docker inspect --format='{{(index (index .NetworkSettings.Ports "8787/tcp") 0).HostPort}}' <CONTAINER ID|NAME>
```

### Get a subsection in JSON format
```shell
$ docker inspect --format='{{json .Config}}' <CONTAINER ID|NAME>
```

### Get environmental variables
```shell
$ docker inspect --format='{{range .Config.Env}}{{println .}}{{end}}' <CONTAINER ID|NAME>
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
* https://docs.docker.com/engine/reference/commandline/inspect/
* https://qiita.com/gold-kou/items/44860fbda1a34a001fc1