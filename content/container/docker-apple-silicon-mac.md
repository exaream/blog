---
date: 2023-02-14
lastmod: ["lastmod"]
title: Docker on Apple Silicon Mac
linkTitle: Apple Silicon Mac
description: How to run Docker on Apple Silicon Mac
tags: ["Apple Silicon M1", "Docker", "Container"]
---

# How to run Docker on Apple Silicon Mac

## Install Rosetta

```shell
% softwareupdate --install-rosetta --agree-to-license
```

## Enable Rosetta

1. Open `Docker Desktop`
1. `Extensions ︙` > `Settings` > `Features in development`
1. Check `Use Rosetta for x86/amd64 emulation on Apple Silicon`

## docker run

Add the following option to `docker run` command.
```shell
--platform linux/x86_64
```

## docker-compose.yml

Add `platform` under container name.
```yml
    platform: linux/amd64
````
