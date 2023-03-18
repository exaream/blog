---
date: 2023-03-18
lastmod: ["lastmod"]
title: zsh
linkTitle: zsh
description: zsh
tags: ["zsh", "brew", "Mac", "macOS"]
---

# zsh

## Terminal

### Set `zsh` as a default shell

Check the default shell
```shell
$ echo $SHELL
/bin/bash
```

Check a list of available shells
```shell
$ cat /etc/shells
/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
```

Change the default shell from `bash` to `zsh`
```shell
$ chsh -s /bin/zsh
```

Copy configurations
```shell
$ cat ~/.bashrc >> ~/.zshrc
$ cat ~/.bash_profile >> ~/.zshrc
```

Please rewrite the configuration for `bash` to the one for `zsh`


### Set prefix match

```shell
vi ~/.zshrc
```
Please write the following and save the file.
```shell
# Prefix Match
autoload -Uz history-search-end
zle -N history-beginning-search-backward-end history-search-end
zle -N history-beginning-search-forward-end history-search-end
bindkey "^[[A" history-beginning-search-backward-end
bindkey "^[[B" history-beginning-search-forward-end
```
Apply the change.
```shell
% source ~/.zshrc
```


### Autosuggestions

Install `zsh-autosuggestions`
```shell
brew install zsh-autosuggestions
echo `source /usr/local/share/zsh-autosuggestions/zsh-autosuggestions.zsh` >> ~/.zshrc
exec zsh
```

## Visual Studio Code

### Set `zsh` as a default shell
1. `Cmd` + `Shift` + `P`
1. Select `Terminal: Select Default Profile` 
1. Select `zsh zsh -l`
