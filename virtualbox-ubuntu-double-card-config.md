---
title: VirtualBox下Ubuntu虚拟机双网卡配置
date: 2017-07-18 10:12
Author: chengz
categories: 每天进步一点点
tags:
 - git
permalink: virtualbox-ubuntu-double-card-config
---

---

昨天在mac上利用VirtualBox 装了Ubuntu Server虚拟机，利用网络配置中的`NAT网络转发 + 仅主机` 启用双网卡以达到单独连外网和单独连接宿主机的目的. 

利用图形界面配置后并不能生效
`/etc/network/interface`如下

```
#The loopback network interface
auto lo
iface lo inet loopback
#The primary network interface
auto enp0s3
iface enp0s3 inet dhcp
```

只有一个网卡`enp0s3` 在working 
在`ll /sys/class/net/` 我们可以到`enp0s8`

```
lrwxrwxrwx  1 root root 0 Aug 16 10:26 enp0s3 -> ../../devices/pci0000:00/0000:00:03.0/net/enp0s3/
lrwxrwxrwx  1 root root 0 Aug 16 10:26 enp0s8 -> ../../devices/pci0000:00/0000:00:08.0/net/enp0s8/
lrwxrwxrwx  1 root root 0 Aug 16 10:26 lo -> ../../devices/virtual/net/lo/
```

需要在`/etc/network/interface`中添加
```
#The user added network interface
auto enp0s8
iface enp0s8 inet dhcp
```
然后执行`sudo ifup enp0s8` 如不生效可重启虚拟机.

### 参考
- <http://blog.csdn.net/kongxx/article/details/43405947>

