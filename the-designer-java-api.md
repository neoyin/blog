---
title: 设计师项目后端API
date: 2017-01-02 16:34
Author: neoyin
categories: 技术流
tags:
 - docker
 - springboot
 - react-native
permalink: the-designer-java-api
---

---

##### 数据准备

###### 搭建Wordpress做为CMS系统

```
apt-get install nginx  
apt-get install php5-fpm
apt-get install php5-mysql
apt-get install php5-gd
```
###### 配置Nginx 

```
server {
	listen 80 default_server;
	listen [::]:80 default_server ipv6only=on;

	root /data/www/wordpress;
	index index.html index.htm index.php;
	access_log /var/log/nginx/access.www.designer.l99.com.log;
	server_name localhost designer.l99.com;

	location / {
		try_files $uri $uri/ =404;
	}
	location ~ \.php$ {
             try_files $uri =404;
             fastcgi_pass unix:/var/run/php5-fpm.sock;
             fastcgi_index index.php;
             fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
             include fastcgi_params;
    }
	location ~.*\.(gif|jpg|jpeg|png|bmp|swf|ico)$ {
        	expires       15d;
   	}
   	location ~.*\.(js|css)?$ {
        	expires       7d;
   	}
}
```
###### 安装wordpress生成数据库

wordpress安装不在这里写了

###### 爬取数据到wordpress数据库

> 这里先不介绍爬虫项目了,有很多JAVA的开源项目可以用。这里是参照了。[WebCollector](https://github.com/CrawlScript/WebCollector) 写了一个简单的.

---

###### wordpress到Mongodb

> 写了一个简单的JAVA程序加脚本去定时按修改时间更新数据
```
ps aux | grep lifeix-designer-w2m | grep -v 'grep' |awk '{print $2}'|xargs kill
cd /data/shell
nohup java -jar lifeix-designer-w2m-1.0-SNAPSHOT.jar &

#每二分钟执行一次
*/2 * * * * dw-to-mongo.sh
```

##### SpringBoot 项目

<https://start.spring.io/> 可以利用spring官网直接生成项目结构，定义好`group`和`Artifact` 并选择所需要的依赖，生成即可。我在这里使用了`jersey`做`rest`构架

生成结构如下:
```
├── mvnw
├── mvnw.cmd
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── lifeix
│   │   │           └── designer
│   │   │               ├── LifeixDesignerPostApplication.java
│   │   └── resources
│   │       └── application.properties
│   └── test
│       └── java
│           └── com
│               └── lifeix
│                   └── designer
│                       └── LifeixDesignerPostApplicationTests.java
```
可以直接运行`LifeixDesignerPostApplication`

##### 项目docker 打包镜像

跳过项目逻辑，在项目main 文件目录创建`docker/Dockerfile`

`Dockerfile`如下

```
FROM java:8-jre VOLUME /tmp ADD app.jar app.jar RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime RUN echo "Asia/Shanghai" > /etc/timezone RUN bash -c 'touch /app.jar' ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

直接利用`maven` 打docker包需要引入[docker-maven-plugin](https://github.com/spotify/docker-maven-plugin) 
在`pom.xml`添加

```
<plugin> 	<groupId>com.spotify</groupId> 	<artifactId>docker-maven-plugin</artifactId> 	<version>0.4.6</version> 	<configuration> 		<dockerHost>${dockerHost}</dockerHost> 		<dockerCertPath>${dockerCertPath}</dockerCertPath> 		<imageName>${docker.image.prefix}/${project.artifactId}</imageName> 		<dockerDirectory>src/main/docker</dockerDirectory> 		<resources> 			<resource> 				<targetPath>/</targetPath> 				<directory>${project.build.directory}</directory> 				<include>${project.build.finalName}.jar</include> 			</resource> 		</resources> 	</configuration> </plugin>
```

由于使用了阿里云的容器集群所以在dockerHost 及dockerCertPath加入了相关信息

##### 镜像及发布

添加完成后使用maven 命令打包

```
mvn clean -Dmaven.test.skip=true -P prod package docker:build -DpushImagetags -DdockerImagetags=$tagnum
//推送镜像到阿里云镜像仓库
docker push registry-internal.cn-beijing.aliyuncs.com/designer/lifeix-designer-post:$tagnum
```

推送至阿里云镜像仓库后在其容器服务的应用列表中创建应用,选择对应的镜像，注意打开对应的端口映射，`springboot`默认的端口号是8080，点击创建，阿里云会自动将应用部署成服务. 

在 容器服务的服务列表中可以看到此服务对应的容器信息。你可以根据暴露的ip测试了。

##### 负载均衡

阿里云在创建集群的时候也会创建一个负载均衡，在域名的解析中添加至负载均衡的ip，你就可以使用域名访问啦(当然域名是需要备案的)

