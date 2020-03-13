---
title: 阿里云搭建Rancher服务
date: 2016-10-25 17:10
Author: neoyin
categories: 技术流
tags: docker,rancher
permalink: aliyun-docker-rancher
---

---

##### 阿里云开通EC2+ RDS
##### 安装Docker
由于我们采用的Ubuntu  安装Docker <https://docs.docker.com/engine/installation/linux/ubuntulinux/>

```
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
#在/etc/apt/sources.list.d/docker.list 添加
deb https://apt.dockerproject.org/repo ubuntu-trusty main
 sudo apt-get update
sudo apt-get install docker-engine
```

Docker安装完成 

##### 安装Rancher Server
- <http://docs.rancher.com/rancher/v1.2/en/installing-rancher/installing-server/>

这里为了之后升级rancher方便使用了单独的mysql数据库

```
docker run -d --restart=unless-stopped -p 8080:8080 \
    -e CATTLE_DB_CATTLE_MYSQL_HOST=$host \
    -e CATTLE_DB_CATTLE_MYSQL_PORT=3306 \
    -e CATTLE_DB_CATTLE_MYSQL_NAME=$dbname \
    -e CATTLE_DB_CATTLE_USERNAME=$username \
    -e CATTLE_DB_CATTLE_PASSWORD=$password \
    rancher/server
```

通过http://$ip:8080 是不是可以访问Rancher 
