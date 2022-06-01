---
date: 2021-07-31
lastmod: ["lastmod"]
title: Vagrant
linkTitle: Vagrant
description: Vagrant
#tags: ["Vagrant"]
draft: true
---

# Vagrant

## Basic

start/create
```shell
$ vagrant up
```
```shell
$ vagrant up sample
```
connect
```shell
$ vagrant ssh sample
```
stop
```shell
$ vagrant halt sample
```
delete
```shell
$ ls -A
.vagrant	Vagrantfile
$ vagrant destroy -f
$ rm -rf Vagrantfile .vagrant
```
