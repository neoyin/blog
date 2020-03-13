---
title: Linux下systemctl命令
date: 2017-04-29 13:59
Author: neoyin
categories: 技术流
tags: linux,systemctl
permalink: linux-command-systemctl
---

---

systemctl命令是系统服务管理器指令，它实际上将 service 和 chkconfig 这两个命令组合到一起

###### 例

```
启动nfs服务
systemctl start nfs-server.service
设置开机自启动 
systemctl enable nfs-server.service
停止开机自启动 
systemctl disable nfs-server.service 
查看服务当前状态 
systemctl status nfs-server.service 
查看所有已启动的服务 systemctl list -units --type=service
```
