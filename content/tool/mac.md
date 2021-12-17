---
date: 2020-01-01
lastmod: ["lastmod"]
title: Mac
description: How to use macOS
tags: ["Mac"]
---

# Mac

## iTerm2

### What's iTerm2
* iTerm2 is a terminal emulator for macOS  
https://iterm2.com/

### How to input to multiple consoles at the same time
* `Shell` > `Broadcast Input` > `Broadcast input to All Panes in Current Tab`

### How to output console log automatically
```shell
$ mkdir -p /Users/`whoami`/iTerm2/
```
* `iTerm2` > `Preferences` > `Profiles`
* Select `Profile Name` for which you want to set the console log
* Click `Session` tab
* Check `Automatically log session input to files in:`
* Check `log plain text`
* Input the directory path

### How to change the background color of iTerm2 for each environment
* Add color schemes to iTerm2 by referring to the following URL.  
  https://github.com/mbadolato/iTerm2-Color-Schemes/blob/master/README.md
* `iTerm2` > `Preferences` > `Profiles` > `General tab`
* Click `+`
* Name `MyDefault`
* Click `Colors tab`
* Select from `Color Presets...`

#### If you change the color schema using shell script
```shell
echo -ne "\033]1337;SetProfile=MyDefault\a"
```

## Box Drive
* How to display Box Drive context menu
  * `System Preferences` > `Extensions`
  * Checl `Box` > `Finder Extensions`

## Siri

### How to disable Siri
https://pc-karuma.net/mac-touch-bar-delete-siri-button/

## Preview
https://support.apple.com/en-us/guide/preview/prvw15636/mac


## How to use two Mac apps side by side in Split View
https://support.apple.com/en-us/HT204948