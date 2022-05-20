---
date: 2021-07-31
lastmod: ["lastmod"]
title: Jenkins
linkTitle: Jenkins
description: Jenkins
#tags: ["Jenkins"]
draft: true
---

# Jenkins

## Install

### CentOS

```shell
sudo wget --no-check-certificate -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install -y jenkins
```
