---
title: Pyflink开发-环境部署
date: 2020-02-25 17:10
Author: neoyin
categories: 技术流
tags:
 - docker
 - flink
 - python
 - pyflink
permalink: pyflink-config-deploy
---

---

## 前言

由于公司项目主要模块为python开发，最好项目都能跑在python环境下 
 公司虽然有flink平台,如果需要公司平台来支持pyflink 不一定有能力和资源去推动做这件事
我们需要自己来维护一个小集群flink环境 满足我们项目需求即可 
最好的方案是实现部署在docker环境中.管理方便,资源最小化

## PyFlink环境准备

1. JDK 1.8+
2. Python 3.5+
3. Flink-1.10.0 

## 本地环境安装 

1. Flink-1.10.0 scala-2.11 到本地并解压 https://archive.apache.org/dist/flink/flink-1.10.0/flink-1.10.0-bin-scala_2.11.tgz

2. pip install apache-flink  完成如下

   注意以下两个模块版本

   ```python
   apache-beam 2.15.0
   apache-flink 1.10.0
   ```

```python
pip list
Package Version
--------------- ----------
apache-beam 2.15.0
apache-flink 1.10.0
avro-python3 1.9.2.1
bottle 0.12.18
certifi 2019.11.28
chardet 3.0.4
cloudpickle 1.2.2
crcmod 1.7
dill 0.2.9
docopt 0.6.2
fastavro 0.21.24
future 0.18.2
grpcio 1.27.2
hdfs 2.5.8
httplib2 0.12.0
idna 2.9
mock 2.0.0
numpy 1.18.1
...
```

安装模块需要比较好的网络 可以使用国内pip源

用户目录pip配置` ~/.config/pip/pip.conf`

```python
[global]
index-url = http://mirrors.aliyun.com/pypi/simple
[install]
trusted-host = mirrors.aliyun.com
```

## Docker集群环境

官方并有一个python环境的flink，需要自己构建一个python环境的flink。

##### 1.构建PyFlink 环境 Docker Image

相关项目代码已发布git如下  https://git.chinawayltd.com/rdp/pyflink-rdp

在原官方Dockerfile中 加了入python环境及pyflink依赖 https://github.com/apache/flink-docker/blob/346809e6cab2ca0ac702fb4d2bf56afb6ee3c437/1.10/scala_2.11-debian/Dockerfile

```dockerfile
# Install python
RUN set -ex; \
apt-get update; \
apt-get -y install python3; \
apt-get -y install python3-pip; \
apt-get -y install python3-dev; \
ln -s /usr/bin/python3 /usr/bin/python; \
ln -s /usr/bin/pip3 /usr/bin/pip
# Install apache-flink
RUN set -ex; \
apt-get update; \
python -m pip install --upgrade pip; \
pip install apache-flink
```

为了加快构建速度这里将 Flink安装包 下载到本地 

```dockerfile
COPY flink-1.10.0-bin-scala_2.11.tgz $FLINK_HOME
RUN tar -xf flink*.tgz --strip-components=1;\
rm flink*.tgz; \
chown -R flink:flink .;
```

##### 2. 构建镜像 `docker build -t pyflink-rdp:v1 .`

```dockerfile
docker images
REPOSITORY TAG IMAGE ID CREATED SIZE
pyflink-rdp v1 2c1f25773d6a 12 hours ago 2.27GB
openjdk 8-jre 11c7adda2eb7 12 days ago 264MB
```

最终整个镜像确实有点大.

##### 3. docker-compose 编排如官网

```dockerfile
version: "1.0"
services:
jobmanager:
image: pyflink-rdp:v1
expose:
- "6123"
ports:
- "8081:8081"
command: jobmanager
environment:
- JOB_MANAGER_RPC_ADDRESS=jobmanager
- FLINK_LOG_DIR=/opt/flink/log
- log.file=/opt/flink/log/jobmanager.log
taskmanager:
image: pyflink-rdp:v1
expose:
- "6121"
- "6122"
depends_on:
- jobmanager
command: taskmanager
links:
- "jobmanager:jobmanager"
environment:
- JOB_MANAGER_RPC_ADDRESS=jobmanager
- FLINK_LOG_DIR=/opt/flink/log
- log.file=/opt/flink/log/taskmanager.log
```

### 4. 启动`docker-compose up -d` 

```shell
Creating pyflink-rdp_taskmanager_1 ... done
Creating pyflink-rdp_jobmanager_1 ... done
```

访问http://$ip:8081验证成功

### 5. 部署

在本地Flink目录

`./flink run -m $ip:8081 -py $path/deploy_demo.py`

##### 6. K8S

https://ci.apache.org/projects/flink/flink-docs-stable/ops/deployment/kubernetes.html

https://ci.apache.org/projects/flink/flink-docs-stable/ops/config.html#taskmanager-memory-jvm-metaspace-size

## 遇到问题

1. **[本地环境]**ModuleNotFoundError: No module named 'grpc'  
   本地python环境有点问题 重新配置了python环境和pip
2. **[本地环境]**google/protobuf/[descriptor_database.cc](http://descriptor_database.cc/):58] file already exists in database:
   暂时将pyarrow从0.14.1 升级至pip install pyarrow==0.15.1 (可能有潜在问题)
   Some synt  actic constructs of Python 3 are not yet fully supported by Apache Beam.



