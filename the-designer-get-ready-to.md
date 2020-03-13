---
title: 设计师项目前期技术准备
date: 2017-01-01 16:34
Author: neoyin
categories: 技术流
tags:
 - docker
 - springboot
 - react-native
permalink: the-designer-get-ready-to
---

---

##### 需求

简单的描述一下项目第一个版本的需求：
1. 展示文章分类，根据分类展示文章列表
2. 展示推荐的设计师相关的精品文章列表及详情.
3. 展示设计师编辑列表，以及设计师发表的文章
4. 运营可添加修改文章内容

以上是一些简单的基本需求。
第一个版本很简单，基本就是读读数据. 

##### 服务器及部署

考虑到后期的运维及扩展，服务器使用了阿里的EC2.及容器服务。

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/25977/cn_zh/1477721882579/1.png "")

介绍可以看一下链<https://help.aliyun.com/document_detail/25977.html?spm=5176.2020520152.201.2.Nt26UB>
这里简单用到了
负载均衡，应用发布，服务调度，镜像仓库

目前只有一个节点

##### 数据的抓取及管理 

项目需要抓取相关的数据并做修改，我不可能自己写一个管理后台，就用`Wordpress`做CMR系统，把所抓取的数据写入Wordpress Mysql系统中去处理. 

##### 生产数据及更新

生产数据这里并不想用Wordpress的Mysql，决定采用`Mongodb`做为生产数据库，
虽然避免了自己写管理系统，但是有数据更新的问题，写了一个定时任务，每一分钟同步一次更新过的数据.

##### 后端API

后端API 微服务化，而且用了`docker`容器服务，所以Java的架构用了`Spring-boot`快速开发，部署及测试也比较方便。

##### 前端mobile

没有用`springmvc` 而且前后端做了分离。本来想采用`Angular2`编写，但是前几个月才会Angular2做了一个项目，回过头来看官网发现Angular2升级改动太大了，而且网站SEO也需要去处理。 所以这里采用了nodejs的MVC框架，简单快速。

##### 客户端

`React-native`是首选了。

---

基本上整个项目的简单架构如此，接下来就是进入实施阶段了。





