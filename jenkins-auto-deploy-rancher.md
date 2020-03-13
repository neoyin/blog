---
title: Jenkins自动构建及通过Rancher API自动发布
date: 2016-10-26 10:10
Author: neoyin
categories: 技术流
tags: docker,rancher,jenkins
permalink: jenkins-auto-deploy-rancher
---

---

##### Jenkins 构建

之前已经搭建了jenkins构建系统，我们在jenkins上构建项目并打包成docker 镜像 发布到阿里云的镜像管理中心。现在需要利用jenkins直接自动发布项目。 

由于Rancher自身有API可以直接调用，这样可以在jenkin编译项目后直接调用Rancher的API 实现一键发布。

##### Rancher API 
1. 先要在`Rancher Server` 中添加一个`API Keys`  一定要保存下来[只会显示一次]
2. 在`Rancher Server` 添加镜像库 
3. 找到要发布的项目的`API查看` 在actions 里 点击`upgrade` 可显示request 

```
curl -u "${ACCESS_KEY}:${SECRET_KEY}" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"inServiceStrategy":null, "toServiceStrategy":null}' \
'http://$ip/v1/projects/$project/services/$service/?action=upgrade'
```

我们需要在`inServiceStrategy` 添加一些发布信息

```
"inServiceStrategy": {
  "launchConfig": {
    "tty":true,
    "vcpu":1,
    "imageUuid":""
  }
}
```

- 注意这里的imageUuid 需要`docker:`开头，这里卡了好久不知道是怎么回事，经过调度才搞明白 

##### Jenkins 发布
在Jenkins构建完成之后的步骤加上一个步骤  发送一个request 给Rancher API  看看是不是OK了。 

---

##### 参考 
- <https://forums.rancher.com/t/upgrade-rancher-service-using-rest-api/1459>
- <https://github.com/etlweather/gaucho>





