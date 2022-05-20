---
date: 2021-07-31
lastmod: ["lastmod"]
title: Kubernetes
linkTitle: Kubernetes
description: Kubernetes
#tags: ["Kubernetes"]
draft: true
---

# Kuberntes

## Install

### macOS
```shell
brew install kubectl
```

```shell
brew install minikube
```

## Basics

```shell
kubectl version
```

```shell
kubectl version --client
```

```shell
kubectl create -f <nanifest file(s)>
```

```shell
kubectl apply -f <nanifest file(s)>
```

```shell
kubectl delete -f <nanifest file(s)>
```

```shell
kubectl get <ObjectType>
```

```shell
kubectl describe <ObjectType> <ObjectName>
```

```shell
kubectl get <ObjectType> <ObjectName>
```

```shell
kubectl cluster-info
```

```shell
kubectl cluster-info dump
```

```shell
minikube start --driver=docker
```

```shell
minikube status
```
