---
title: Mac 10.12 无法允许“任何来源” 的应用
date: 2016-10-14 13:29
Author: neoyin
categories: 技术流
tags:
 - macos
permalink: mac-os-any-resource
---

---

最近Mac系统升级了 10.12 在安装app的时候发现系统偏好设置的“安全与隐私”中没有了允许“任何来源”App 的选项。一些从网上下载的app无法安装了。

查了一下 需要关闭 Gatekeeper(一些从互联网下载和安装的 app 可能对 Mac 产生不利影响，Gatekeeper 帮助保护 Mac 免受此类 app 的影响。)

命令如下:
`sudo spctl --master-disable`

