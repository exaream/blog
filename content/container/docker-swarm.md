---
date: 2020-01-01
lastmod: ["lastmod"]
title: Docker Swarm
linkTitle: Docker Swarm
description: Docker Swarm
tags: ["docker swarm", "docker stack", "Docker", "Container"]
---
# Docker Swarm

### Swarm クラスタを初期化
```shell
docker swarm init [options]
```

### Swarm クラスタに node もしくは manager を追加
```shell
docker swarm join [options] managerホスト:ポート
```

### Swarm クラスタから node もしくは manager を離脱
```shell
docker swarm leave [options]
```

### Swarm クラスタに Stack をデプロイ
```shell
docker stack deploy [options] Stack名
```

### Swarm クラスタにデプロイされている Stack 一覧を表示
```shell
docker stack (ls|list) [options]
```

### Stack 内で実行されているタスクを表示
```shell
docker stack ps [options] Stack名
```

### Swarm クラスタからStackを削除
```shell
docker stack (rm|remove|down) [options] Stack名...
```

### Stack 内のService一覧を表示
```shell
docker stack services [options] Stack名
```
