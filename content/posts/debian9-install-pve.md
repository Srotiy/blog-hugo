---
title: "Debian9安装pve虚拟环境"
date: 2018-04-01T11:17:18+08:00
draft: false
---
## debian9安装pve

官方文档

> https://pve.proxmox.com/wiki/Installation

设置/etc/hosts

本机ip设置为主机名 如，网卡ip为 192.168.24.100 主机名为srotiy-server 设置192.168.24.100 srotiy-server

### 加入源

```bash
vim /etc/apt/sources.list.d/pve-install-repo.list

deb http://download.proxmox.com/debian/pve stretch pve-no-subscription
```

### 导入gpg密钥

```
wget http://download.proxmox.com/debian/proxmox-ve-release-5.x.gpg -O /etc/apt/trusted.gpg.d/proxmox-ve-release-5.x.gpg
```

### 安装

apt update

apt dist-upgrade

apt install proxmox-ve postfix open-iscsi

安装完后，确定无报错，重启宿主机 重启后， 访问 https://192.168.24.100:8006 若无法访问检查防火墙等 检查是否为https 默认登陆账号密码为root用户及root密码