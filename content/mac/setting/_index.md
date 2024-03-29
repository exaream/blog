---
date: 2020-01-01
lastmod: ["lastmod"]
title: Mac Setting
LinkTitle: Setting
description: Mac Setting
tags: ["Mac", "Setting", "macOS"]
---

# Mac Setting

## Safari

### Empty Caches

1. `Safari` > `Preferences`  
  ![This is a image](safari-preferences.png)
2. `Advanced`
3. Check `Show Develop menu in menu bar`
  ![This is a image](safari-develop-menu.png)
4. `Develop` > `Empty caches`  
  ![This is a image](safari-empty-caches.png)

### Proxy Settings
Switch On
1. `Safari` > `Preferences`  
  ![This is a image](safari-preferences.png)
2. `Advanced`  
3. `Change Settings...`
  ![This is a image](safari-change-settings.png)
4. Select `Proxies` tab
    1. Check `Web Proxy (HTTP)` and enter `proxy domain` : `proxy port`
    2. Check `Secure Web Proxy (HTTP)` and enter `proxy domain` : `proxy port`
    3. Click `OK`
    ![This is a image](mac-proxy-settings.png)

5. Click `Apply`  
  ![This is a image](mac-proxy-apply.png)
6. Restart `Safari`  
  (It will take some time for the change to take effect)


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

## Port
How to find a process on a specific port.
```shell
$ sudo lsof -i:<Port Number>
```
e.g.
```shell
$ sudo lsof -i:5432
```

How to kill the process running on the specific port.

```shell
$ kill -9 <PID>
```
