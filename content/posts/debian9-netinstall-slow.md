---
title: "Debian9安装pve虚拟环境"
date: 2018-02-13T11:17:18+08:00
draft: false
---
# 原因

安装过程会连接安全服务器然后更新内核balabalabala……..

# 解决

我这里使用了mini.iso

```
http://mirrors.ustc.edu.cn/debian/dists/stable/main/installer-amd64/current/images/netboot/mini.iso
```

新系统会挂载在安装系统的/target目录下

等待配置完apt后

在软件包统计后

进入选择软件时

按键盘的alt+f2进入console

```bash
chroot /target
nano /etc/apt/sources.list
```

把debian-security的url换为国内镜像源

然后….

```bash
chroot /target
apt update
```

按alt+f1返回安装界面，愉快安装