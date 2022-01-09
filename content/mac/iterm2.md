---
date: 2020-01-01
lastmod: ["lastmod"]
title: iTerm2
description: How to use iTerm2
tags: ["iTerm2"]
---

# iTerm2

## What's iTerm2
* iTerm2 is a terminal emulator for macOS  
https://iterm2.com/

## Shortcuts

|command|description|
|---|---|
|`command` + `N`|New Window|
|`command` + `T`|New Tab|
|`command` + `return`|Maximize Window|
|`command` + (`◀` or `▶`)|Move Tab|
|`shift` + `command` + `D`|Split Horizontally with Current Profile|
|`command` + `D`|Split Vertically with Current Profile|
|`command` + `F`|Find|
|`option` + `command` + `F`|Replace|
|`shift` + `command` + `F`|Find in Files|
|`shift` + `command` + `H`|Replace in Files|

## How to increase scrollback lines
* `iTerm2` > `Preferences` > `Profiles`
* Select a profile from `Profile Name`
* Click `Terminal` tab
* Change `Scrollback lines:` or check `Unlimited scrollback`

## How to input to multiple consoles at the same time
* `Shell` > `Broadcast Input` > `Broadcast input to All Panes in Current Tab`
![This is a image](../static/iterm2-broadcast-all-panes.png)

## How to output console log automatically
```shell
$ mkdir -p /Users/`whoami`/iTerm2/
```
* `iTerm2` > `Preferences` > `Profiles`
* Select a profile from `Profile Name`
* Click `Session` tab
* Check `Automatically log session input to files in:`
* Check `log plain text`
* Input the directory path

## How to change the background color of iTerm2 for each environment
* Add color schemes to iTerm2 by referring to the following URL.  
  https://github.com/mbadolato/iTerm2-Color-Schemes/blob/master/README.md
* `iTerm2` > `Preferences` > `Profiles` > `General tab`
* Click `+`
* Name `MyDefault`
* Click `Colors tab`
* Select from `Color Presets...`

### If you change the color schema using shell script
```shell
echo -ne "\033]1337;SetProfile=MyDefault\a"
```