---
date: 2023-08-29
lastmod: ["lastmod"]
title: PlantUML
LinkTitle: PlantUML
description: PlantUML
tags: ["PlantUML"]
---

# PlantUML

## Install

```shell
% brew update
% brew doctor
```

Java
```shell
% brew install java
% echo 'export PATH="/usr/local/opt/openjdk/libexec/openjdk.jdk/Contents/Home/bin:$PATH"' >> ~/.zshrc
% source ~/.zshrc
% which java    
/usr/local/opt/openjdk/libexec/openjdk.jdk/Contents/Home/bin/java
% java --version
openjdk 20.0.1 2023-04-18
OpenJDK Runtime Environment Homebrew (build 20.0.1)
OpenJDK 64-Bit Server VM Homebrew (build 20.0.1, mixed mode, sharing)
```

PlantUML
```shell
% brew install plantuml
% which plantuml
/usr/local/bin/plantuml
% plantuml -version
PlantUML version 1.2023.10 (Thu Jul 13 00:54:07 JST 2023)
(GPL source distribution)
Java Runtime: OpenJDK Runtime Environment
JVM: OpenJDK 64-Bit Server VM
Default Encoding: UTF-8
Language: en
Country: JP
 
PLANTUML_LIMIT_SIZE: 4096

Dot version: dot - graphviz version 8.1.0 (20230707.0739)
Installation seems OK. File generation OK
```
