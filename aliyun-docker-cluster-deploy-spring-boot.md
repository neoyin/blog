---
title: 阿里云容器服务部署SpringBoot 服务
date: 2016-12-19 16:34
Author: neoyin
categories: 技术流
tags: docker,springboot
permalink: aliyun-docker-cluster-deploy-spring-boot
---

---

之前在阿里云上使用Rancher来管理容器和部署项目，但是阿里云上也有自己的容器服务，也来试试看

##### 创建容器集群


##### SpringBoot Build 镜像

在`pom.xml`中引入plugin

```
<plugin>
	<groupId>com.spotify</groupId>
	<artifactId>docker-maven-plugin</artifactId>
	<version>0.4.6</version>
	<configuration>
		<dockerHost> docker Server地址  如果本地则不需要 </dockerHost>
		<dockerCertPath> 证书文件路径  </dockerCertPath>
		<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
		<dockerDirectory> Dockerfile路径 </dockerDirectory>
		<resources>
			<resource>
				<targetPath>/</targetPath>
				<directory>${project.build.directory}</directory>
				<include>${project.build.finalName}.jar</include>
			</resource>
		</resources>
	</configuration>
</plugin>
```

在`dockerDirectory`创建Dockerfile

```
FROM java:8-jre
VOLUME /tmp
ADD app.jar app.jar
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
RUN echo "Asia/Shanghai" > /etc/timezone
RUN bash -c 'touch /app.jar'
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

#app.jar 为 ${project.build.finalName}

```

利用`maven` 打包docker 镜像

```
mvn -Dmaven.test.skip=true clean package docker:build 
```

##### 推送镜像到阿里云镜像中心

登录阿里云docker registry

```
docker login --username=$email registry.cn-xxx.aliyuncs.com
docker tag [ImageId] registry.cn-xxx..aliyuncs.com/$namespace/$project:[镜像版本号]
docker push registry.cn-xxx.aliyuncs.com/$namespace/$project:[镜像版本号]
```

###### 启动应用

在阿里云容器服务的应用中创建应用，选择刚刚推送到阿里云中的镜像,映射8080端口 即可创建应该，应用会自动部署相关服务到各节点。

---

##### 参考

- <https://help.aliyun.com/product/25972.html?spm=5176.doc25984.3.1.S0ceHo>
- <https://my.oschina.net/u/3023307/blog/783544>
- <https://github.com/spotify/docker-maven-plugin>



