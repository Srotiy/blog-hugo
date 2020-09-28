---
title: "解决netplan的dns问题"
date: 2018-11-17T11:17:18+08:00
draft: false
---
# 问题

Ubuntu启用新的网络管理程序Netplan
Dhcp获取到的dns有问题，不能用，要替换
查了资料替换后，dhcp获取到的dns依旧无法删除，有一定几率无法解析

# 解决

也懒得换ifupdown
在netplan下，resolv.conf只是个符号链接
一把梭 直接把`/etc/resolv.conf`写死,不用本地缓存了
`rm -rf /etc/resolv.conf`
`echo nameserver 1.0.0.1 > /etc/resolv.conf`
`echo nameserver 8.8.8.8 >> /etc/resolv.conf`