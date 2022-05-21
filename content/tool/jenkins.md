---
date: 2021-07-31
lastmod: ["lastmod"]
title: Jenkins
linkTitle: Jenkins
description: Jenkins
tags: ["Jenkins"]
---

# Jenkins

## Install

### Docker Compose

1. `docker-compose.yml`  
  https://hub.docker.com/_/jenkins


```yml
version: "3"
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins:latest
    ports:
      - 8080:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home

```
2. Run the following.  
  Do NOT use `-d` option to confirm the initial admin password displayed in the terminal.
```shell
$ docker-compose up
```

```shell
# Other ways to confirm the initial admin password
$ docker logs jenkins | less
*************************************************************
*************************************************************
*************************************************************
 
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:
 
XXXXXXXXXXXXXXXXXXXXXXXXXXX (You can see the password here)
 
This may also be found at: /var/jenkins_home/secrets/initialAdminPassword
 
*************************************************************
*************************************************************
*************************************************************

or 

$ docker container ls
$ docker container exec -it jenkins sh
$ cat /var/jenkins_home/secrets/initialAdminPassword
```
3. Access [http://localhost:8080](http://localhost:8080)
    - `Unlock Jenkins`
        - `Administrator passwprd`: Enter the admin initial password.
        - `Continue`
    - `Customize Jenkins`
        - `Install suggested plugins` (if you are a beginner)
    - `Create First Admin User`
        - `Skip and continuue as admin`

### CentOS

```shell
sudo wget --no-check-certificate -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install -y jenkins
```

## Plugin

1. [http://localhost:8080](http://localhost:8080)
2. `Dashboad` > `Manage Jenkins` > `Manage Plugins`
3. `Installed`
  -  The following are already installed if you choose `Install suggested plugins` previous step.
    - `Mailer`
    - `Build With Parameter`
4. `Available`
  - Find and select the following using a search box.
    - `Rebuilder`
    - `Blue Ocean`
    - `Office 365 Connector`
    - `Maven Integration`
  - `Install without restart`

## Job

### Pipeline
- [Jenkins Pipline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax/)

e.g.
```shell
pipeline {
    agent any
    stages {
        stage('Hello') {
            steps {
                echo 'Hello'
            }
        }
        stage('World(SKIP)') {
            when {
                expression { params.SKIP == false }
            }
            steps {
                echo 'World'
            }
        }
    }
}
```