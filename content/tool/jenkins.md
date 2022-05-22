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

- [How to Install and Run Jenkins With Docker Compose](https://www.cloudbees.com/blog/how-to-install-and-run-jenkins-with-docker-compose)

1. Make `docker-compose.yml`  
  https://hub.docker.com/_/jenkins


```yml
version: '3.8'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins:latest
    privileged: true
    ports:
      - 8080:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home

```
2. Run the following.  
```shell
$ docker-compose up -d
```
3. Confirm the admin initial password.
```shell
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
```
or 
```shell
$ docker container ls
$ docker container exec -it jenkins sh
$ cat /var/jenkins_home/secrets/initialAdminPassword
```

4. Access [http://localhost:8080](http://localhost:8080) .
    - `Unlock Jenkins` page.
        - Enter the admin initial password in `Administrator passwprd` .
        - Click `Continue` .
    - `Customize Jenkins` page.
        - Click `Install suggested plugins` (if you are a beginner).
    - `Create First Admin User` page.
        - Click `Skip and continuue as admin` .

### CentOS

```shell
sudo wget --no-check-certificate -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install -y jenkins
```

## Jenkins Agent

### Generate SSH keys
1. Generate the private key and the public key.

```shell
$ ssh-keygen -t ed25519 -f jenkins_agent
Generating public/private ed25519 key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

```shell
$ ls -al jenkins_agent*
```

- `jenkins_agent` (private key)
- `jenkins_agent.pub` (public key)

2. Confirm the strength.
```shell
$ ssh-keygen -l -f jenkins_agent
256 SHA256:XXXXXXXXXXXXXX XXXXXX@XXXXXX (ED25519)
```

### Set the private key in Jenkins
1. Access [http://localhost:8080](http://localhost:8080) and login.
2. Move to `Dashboad` > `Manage Jenkins` > `Manage Credentials` .
3. Click `Jenkins` under `Stores scoped to Jenkins` .
4. Click `Global credentials (unrestricted)` .
5. Click `Add Credentials` in left menu.
6. Set options.
    - Select `SSH Username with private key` .
    - Select `System (Jenkins and nodes only)` from `Scope` .
      - This means the key can NOT be used for jobs.
    - Enter `jenkins_agent` for `ID` .
    - Enter `Jenkins agent key` for `Description` .
    - Enter `jenkins` for `Username` .
    - Select `Enter directly` of `Provate key` .
    - Click `Add` .
    - Enter the contents of the private key.
      Your can see it by executing following `cat jenkins_agent` .
    - Click `OK` .

### Set the public key
1. Add `agent` container to `docker-compose.yml` .
    - https://hub.docker.com/r/jenkins/ssh-agent
    - Do NOT enclose the contents of the public key in quotes.
```yml
version: '3.8'
services:
  jenkins:
    container_name: jenkins
    image: jenkins/jenkins:latest
    privileged: true
    ports:
      - 8080:8080
    volumes:
      - ./jenkins_home:/var/jenkins_home
agent:
  image: jenkins/ssh-agent:jdk11
  privileged: true
  container_name: agent
  expose:
    - 22
  environment:
    - JENKINS_AGENT_SSH_PUBKEY=ssh-ed25519 XXXXXXXXXXXXXX XXXXXX@XXXXXX (Please write the public key here)
```

2. Restart docker compose.
```shell
$ docker-compose down
$ docker-compose up -d
```

### Add new node
1. Access [http://localhost:8080](http://localhost:8080) and login.
2. Move to `Dashboad` > `Manage Jenkins` > `Manage Nodes and Clouds` .
3. Click `New Node` in the menu on the left.
    - Enter `jenkins_agent` in `Name` .
    - Select `Permanent Agent` in `Type` .
    - Click `Create` .
    - Enter `/home/jenkins/agent` in `Remote root directory` .
    - Select `Use this node as much as possible` in `Usage` .
    - Select `Launch agents via SSH` in `Launch method` .
    - Select `Jenkins(Jenkins Agent Key)` in Credentials.
    - Select `Non verifying Verification Strategy` in `Host Key Verification Strategy` .
    - Click `Advanced` on the right.
    - Enter `/usr/local/openjdk-11/bin/java` in `JavaPath` .
    - Click `Save` .
4. Click `jenkins-agent` in node list.
5. Click `Log` in the menu on the left. .
6. It is OK if you see `Agent successfully connected and online` .

## Plugin

1. Access [http://localhost:8080](http://localhost:8080) and login.
2. Move to `Dashboad` > `Manage Jenkins` > `Manage Plugins` .
3. Click `Installed` .
    -  Confirm that the following are already installed  if you choose `Install suggested plugins` previous step.
        - `Mailer`
        - `Build With Parameter`
4. Click `Available` .
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
