---
title: Pyflink接收kafka消息
date: 2020-02-28 17:10
Author: neoyin
categories: 技术流
tags:
 - docker
 - flink
 - python
 - pyflink
permalink: pyflink-kafka
---

---

##  Kafka依赖

如下

- flink-connector-kafka-0.9_2.11-1.8.1.jar   
- flink-connector-kafka-base_2.11-1.10-SNAPSHOT.jar
- flink-json-1.10-SNAPSHOT.jar 
- kafka-clients-0.9.0.1.jar

> 注意:这里kafka相关的版本号为0.9 与公司kafka服务器版本相同

## 环境部署kafka依赖

1. 本地开发部署
   需要将以上jar包 copy至python flink 环境中 $python_module/site-packages/pyflink/lib 目录下
   /usr/local/lib/python3.7/site-packages/pyflink/

2. 服务器环境部署
   将以上jar包copy至 $flink_home/lib 目录下

3. Docker环境部署

   [pyflink-rdp](https://git.chinawayltd.com/rdp/pyflink-rdp) 项目Dockerfile如下 添加jar包

```dockerfile
COPY flink-1.10.0-bin-scala_2.11.tgz $FLINK_HOME
RUN ``tar` `-xf flink*.tgz --strip-components=1;\
rm` `flink*.tgz; \
chown` `-R flink:flink .;
COPY jlib/* $FLINK_HOME``/lib/
```

##  Kafka 开发准备

​    这直接用公司kafka集群平台测试
```
kfk_services = "node1.test.com:9092,node2.test.com:9092,node3.test.com:9092"
kfk_consumer_topic = "pyflink-test";
kfk_consumer_groupid ="test";
kfk_producter_topic ="pyflink-test-result";
 
kfkConsumer = Kafka().version("0.9").topic(kfk_consumer_topic).start_from_latest()\
.property("group.id",kfk_consumer_groupid)\
.property("bootstrap.servers", kfk_services)\
.property("zookeeper.connect","")
```

注意: version为0.9 与服务器版本保持一致

```python
st_env.connect(kfkConsumer) \
.with_format(Json()
.fail_on_missing_field(``True``)
.json_schema(
"{"
" type: 'object',"
" properties: {"
" type: {"
" type: 'string'"
" },"
" carnum: {"
" type: 'string'"
" },"
" time: {"
" type: 'string',"
" format: 'date-time'"
" }"
" }"
"}"
)
)
```

需要将数据json格式化 json schema 见官方文档 http://json-schema.org/specification.html

```python
kfkProducter = Kafka().version("0.9").topic(kfk_producter_topic) \
.property("bootstrap.servers", kfk_services) \
.property("zookeeper.connect","")
  
st_env.connect(kfkProducter.sink_partitioner_fixed()) \
.with_format(Json().fail_on_missing_field(True).json_schema(
"{"
" type: 'object',"
" properties: {"
" carnum: {"
" type: 'string'"
" },"
" count: {"
" type: 'integer'"
" },"
" t: {"
" type: 'string'"
" }"
" }"
"}"
))\
.with_schema(Schema()
.field('carnum', DataTypes.STRING())
.field('count', DataTypes.INT())
.field("t",DataTypes.STRING())
) \
.register_table_sink('mySink')
```

```
 st_env.scan('source') \
		.window(Session.with_gap("30.minutes").on("rowtime").alias("w"))\
		.group_by("w,carnum")\
		.select("carnum,count(carnum),carnum as t")\
		.insert_into('mySink')
```

## 部署

部署方式不变如上篇文章 

## 注意问题

公司用的pyflink版本为1.10 对应的kafka为0.11 需要与kafka服务端保持一致 