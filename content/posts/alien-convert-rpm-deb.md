---
title: "使用Alien转换rpm及deb包"
date: 2018-06-23T11:17:18+08:00
draft: false
---

# 安装

```
sudo apt update`
`sudo apt install alien
```

# 使用

这里我们以java的rpm包为例
`sudo alien jre-8u171-linux-x64.rpm -c -d`
`jre1.8_1.8.0171-1_amd64.deb generated`
`sudo dpkg -i jre1.8_1.8.0171-1_amd64.deb`

结果。 `Selecting previously unselected package jre1.8. (Reading database ... 37875 files and directories currently installed.) Preparing to unpack jre1.8_1.8.0171-1_amd64.deb ... Unpacking jre1.8 (1.8.0171-1) ... Setting up jre1.8 (1.8.0171-1) ... Unpacking JAR files... plugin.jar... javaws.jar... deploy.jar... rt.jar... jsse.jar... charsets.jar... localedata.jar... /usr/sbin/alternatives not available, skip registering alternatives for java...`

