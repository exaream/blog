---
date: 2021-07-31
lastmod: ["lastmod"]
title: Vagrant
linkTitle: Vagrant
description: Vagrant
#tags: ["Vagrant"]
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

## Uninstall

### Remove the Vagrant Program
On macOS:
```shell
$ sudo rm -rf /opt/vagrant /usr/local/bin/vagrant
$ sudo pkgutil --forget com.vagrant.vagrant
```

On Linux:
```shell
$ rm -rf /opt/vagrant
$ rm -f /usr/bin/vagrant
```
On Windows:
```
Uninstall using the add/remove programs section of the control panel
```
### Remove User Data

On macOS or Linux:
```shell
$ rm -rf ~/.vagrant.d
```
On Windows  
Remove the following directory.
```
C:\Users\YourUsername\.vagrant.d
```
