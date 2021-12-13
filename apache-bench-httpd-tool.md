---
title: ApacheBench(ab)压力测试工具
date: 2021-10-23 17:10
Author: neoyin
categories: 技术流
tags:
 - httpd-tools
permalink: apache-bench-httpd-tool

---


##### AB的简介

　　ab是apachebench命令的缩写。

　　ab是apache自带的压力测试工具。ab非常实用，它不仅可以对apache服务器进行网站访问压力测试，也可以对或其它类型的服务器进行压力测试。比如nginx、tomcat、IIS等

##### ab的原理

　　ab的原理：ab命令会创建多个并发访问线程，模拟多个访问者同时对某一URL地址进行访问。它的测试目标是基于URL的，因此，它既可以用来测试apache的负载压力，也可以测试nginx、lighthttp、tomcat、IIS等其它Web服务器的压力。

　　ab命令对发出负载的计算机要求很低，它既不会占用很高CPU，也不会占用很多内存。但却会给目标服务器造成巨大的负载，其原理类似CC攻击。自己测试使用也需要注意，否则一次上太多的负载。可能造成目标服务器资源耗完，严重时甚至导致死机

##### ab的安装

　　安装命令：`yum -y install httpd-tools`

##### ab的使用

　　常用方式：`ab -c 500 -n 5000 http://localhost/`
- -n 在测试会话中所执行的请求个数（总数）
- -c 一次产生的请求个数（单次请求次数）

```shell
[root@H__D /]# ab -c 500 -n 5000 http://localhost/
This is ApacheBench, Version 2.3 <$Revision: 1430300 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient)
Completed 500 requests
Completed 1000 requests
Completed 1500 requests
Completed 2000 requests
Completed 2500 requests
Completed 3000 requests
Completed 3500 requests
Completed 4000 requests
Completed 4500 requests
Completed 5000 requests
Finished 5000 requests


Server Software:        nginx/1.15.3        #测试服务器的名字
Server Hostname:        localhost            #请求的URL主机名
Server Port:            80                          #请求端口

Document Path:          /                        #请求路径
Document Length:        613 bytes          #HTTP响应数据的正文长度

Concurrency Level:      500                    #并发用户数，这是我们设置的参数之一
Time taken for tests:   0.407 seconds     #所有这些请求被处理完成所花费的总时间 单位秒
Complete requests:      5000                  #总请求数量，这是我们设置的参数之一
Failed requests:        0                           #表示失败的请求数量
Write errors:           0                            
Total transferred:      4230000 bytes       #所有请求的响应数据长度总和。包括每个HTTP响应数据的头信息和正文数据的长度
HTML transferred:       3065000 bytes     #所有请求的响应数据中正文数据的总和，也就是减去了Total transferred中HTTP响应数据中的头信息的长度
Requests per second:    12284.44 [#/sec] (mean)    #吞吐量，计算公式：Complete requests/Time taken for tests  总请求数/处理完成这些请求数所花费的时间
Time per request:       40.702 [ms] (mean)             #用户平均请求等待时间，计算公式：Time token for tests/（Complete requests/Concurrency Level）。处理完成所有请求数所花费的时间/（总请求数/并发用户数）     
Time per request:       0.081 [ms] (mean, across all concurrent requests)    #服务器平均请求等待时间，计算公式：Time taken for tests/Complete requests，正好是吞吐率的倒数。也可以这么统计：Time per request/Concurrency Level
Transfer rate:          10149.06 [Kbytes/sec] received。  #表示这些请求在单位时间内从服务器获取的数据长度，计算公式：Total trnasferred/ Time taken for tests，这个统计很好的说明服务器的处理能力达到极限时，其出口宽带的需求量。

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   0.8      1       6
Processing:     3    5   7.9      5     399
Waiting:        0    4   7.9      4     399
Total:          4    7   8.0      7     404
WARNING: The median and mean for the initial connection time are not within a normal deviation
        These results are probably not that reliable.

Percentage of the requests served within a certain time (ms)
  50%      7               #50%用户请求在7ms内返回
  66%      7               #60%用户请求在7ms内返回
  75%      7
  80%      7
  90%      7
  95%      7
  98%     12              #98%用户请求在12ms内返回
  99%     12
 100%    404 (longest request) 
```



##### 参考

- https://www.cnblogs.com/h--d/p/9903959.html
