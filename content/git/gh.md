---
date: 2023-03-19
lastmod: ["lastmod"]
title: gh
linkTitle: gh
description: gh is the official command-line tool for GitHub. It provides an easy-to-use interface for managing pull requests, issues, repositories, and more.
tags: ["gh", "GitHub"]
---

# `gh`

`gh` is the official command-line tool for GitHub. It provides an easy-to-use interface for managing pull requests, issues, repositories, and more. 

## Install

### macOS

```shell
% brew install gh
```

## Log in

### Get new token on GitHub

1. Access https://github.com/settings/tokens
1. Click `Generate new token` > `Generate new token (classic)`
1. Fill in some fields in a form (enter any name in `Note`)
1. Click `Generate new token`
1. Copy new token

### Log in on the terminal

```shell
% gh auth login
? What account do you want to log into? GitHub.com
? What is your preferred protocol for Git operations? HTTPS
? Authenticate Git with your GitHub credentials? Yes
? How would you like to authenticate GitHub CLI? Paste an authentication token
Tip: you can generate a Personal Access Token here https://github.com/settings/tokens
The minimum required scopes are 'repo', 'read:org', 'workflow'.
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol https
✓ Configured git protocol
✓ Logged in as exaream
```

## Configure

### Update `~/.zshrc`

```shell
% echo '# gh' >> ~/.zshrc 
% echo 'eval "$(gh completion -s zsh)"' >> ~/.zshrc
% source ~/.zshrc
```

## How to use

### Manage pull requsts using `gh pr`
List pull requests
```shell
% gh pr list
```

List pull requests for merge to `main` branch
```shell
% gh pr list -B main
```

List pull requests related to your account
```shell
% gh pr list -s all -a your-account-name
```

Create an alias to list yoru pull requests
```shell
% gh alias set mypr "pr list -s all -a your-account-name"
% gh mypr
```

Create a pull request from current branch to default branch
```shell
% gh pr create
```

Create a pull request from dev to main
```shell
% gh pr create -H dev -B main
```

Create a pull request and view on web
```shell
% gh pr create -w 
```

Merge a pull request that belongs to the current branch
```shell
# Confirm the number of target pull request
% gh pr list
# Merge
% gh pr merge 3
```

### Manage repositories using `gh repo`

Create a repository
```shell
% mkdir sample-repo
% gh repo create
```
Confirm a created repository
```shell
% git remote -v
```

Create a public repository with specified name
```shell
% gh repo create sample-public-repo --public -d 'Sample public repository'
```

Create a private repository with specified name
```shell
% gh repo create sample-private-repo --private -d 'Sample private repository'
```

View the info of a repository in current directory on your local environment
```shell
% gh repo view
```

View a repository's information
```shell
% gh repo view foo/bar
```
