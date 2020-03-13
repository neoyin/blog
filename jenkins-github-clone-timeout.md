---
title: 阿里云Jenkins从Github构建项目超时
date: 2016-10-28 10:10
Author: neoyin
categories: 技术流
tags: jenkins,github
permalink: jenkins-github-clone-timeout
---

---

之前阿里云Ecs 的带宽是按流量计费，官网上给出的最大峰值为100M/s 但是从阿里云上clone Github代理经常性的最大速度5kb/s  而Jenkins在拉取代码时设置了10分钟就会超时

```
Fetching changes from the remote Git repository
 > git config remote.origin.url github.com/xx.git # timeout=10
```

这是一个非常蛋疼的问题，虽然可以配置一些VPN来解决这个问题，但是太过麻烦，
项目仅第一次拉取时花费时间会长，所以还是换个别的解决方案

可以在Jenkins启动时添加一些设置
`java -Dorg.jenkinsci.plugins.gitclient.Git.timeOut=60 -jar jenkins.war`
让它的超时时间变长，这样暂时解决此问题
可以在 `/etc/default/jenkins`文件中修改


- 不知道为什么阿里云访问github会这些慢，我在家里的网络clone github代码都比阿里要快，是不是阿里做了些什么设置？

---

##### 参考
- <https://issues.jenkins-ci.org/browse/JENKINS-20445>
- <https://yq.aliyun.com/articles/36744>