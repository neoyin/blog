---
title: Linux中安装jenkins2
date: 2016-10-13 09:29
Author: neoyin
categories: 技术流
tags:
 - linux
 - jenkins
permalink: linux-jenkins-install
---

---

##### 安装

在Ubuntu安装Jenkins 如下
```
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```
对于Jenkins的配置可以在 `/etc/init.d/jenkins`  `/etc/default/jenkins`中配置 

Jenkins2在启动时根据用户的选择自动安装插件，但是这些插件也会经常失败。
可以到`$jenkins_home/plugins`目录下直接`wget`下载相关插件后重启即可。 

jenkins2插件包目录<http://updates.jenkins-ci.org/download/plugins/>

##### 过程
设置email notification 出现`Could not connect to SMTP`
Java 1.8 安全机制问题
- <http://www.jianshu.com/p/5ba3bde60f21>
- <http://blog.smoker.cc/java/20160821.html>

##### 参考
- <https://jenkins.io/doc/book/getting-started/installing/>
- <http://pkg.jenkins-ci.org/debian/>
- <http://updates.jenkins-ci.org/download/plugins/>
- <http://www.jianshu.com/p/5ba3bde60f21>
- <http://blog.smoker.cc/java/20160821.html>
- <http://blog.csdn.net/wangmuming/article/details/22925357>