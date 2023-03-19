---
date: 2023-03-19
lastmod: ["lastmod"]
title: Command-line tools of GitHub
linkTitle: Command-line tools of GitHub
description: gh & ghq are command-line tools that make it easy to manage GitHub from the terminal.
tags: ["gh", "ghq", "GitHub"]
---

# Command-line tools of GitHub

## `gh` command

`gh` is the official command-line tool for GitHub. It provides an easy-to-use interface for managing pull requests, issues, repositories, and more. 

### Get token on GitHub

1. Access https://github.com/settings/tokens
1. Click `Generate new token` > `Generate new token (classic)`
1. Fill in some fields in a form (enter any name in `Note`)
1. Click `Generate new token`
1. Copy new token

### Install on macOS

```shell
% brew install gh
```

### Log in GitHub from the terminal on macOS

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

### Update `~/.zshrc`

```shell
% echo '# gh' >> ~/.zshrc 
% echo 'eval "$(gh completion -s zsh)"' >> ~/.zshrc
% source ~/.zshrc
```

### How to use `gh` command

List pull requests of your account
```shell
% gh pr list -s all -a your-account-name
```

Create an alias to list yoru pull requests
```shell
% gh alias set mypr "pr list -s all -a your-account-name"
% gh mypr
```

Create a repository
```shell
gh repo create sample-repo --public -d 'Sample Repository'
```

Create a pull request
```shell
gh pr create -w 
```

## `ghq` command

`ghq` is a command-line tool for managing local clones of Git repositories.

### Install `ghq`

```shell
% brew install ghq
```
### Configure

```shell
% ghq root /path/to/your/dir/
% git config --global ghq.root /path/to/your/dir/
```

### How to use `ghq` command

List repositories
```shell
ghq list
```
Clone a repository
```shell
ghq get github.com/foo/bar
```

Delete a repository
```shell
ghq delete github.com/foo/bar
```
