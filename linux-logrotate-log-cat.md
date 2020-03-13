---
title: Linux下Logrotate切割日志
date: 2015-11-04 21:13
Author: neoyin
categories: 技术流
tags:
 - logrotate
permalink: linux-logrotate-log-cat
---

***

Logrotate程序是一个Linux自带日志文件管理工具。使用logrotate轮询日志很方便，配置也很简单。

在`/etc/logrotate.d/` 下已有关于nginx的配置
打开如下:
```
/var/log/nginx/*log {
    daily
    rotate 10
    missingok
    notifempty
    compress
    sharedscripts
    postrotate
        /bin/kill -USR1 $(cat /var/run/nginx.pid 2>/dev/null) 2>/dev/null || :
    endscript
}

# /var/log/nginx/*log：需要轮询日志路径
# daily：每天轮询
# rotate 10：保留最多10次滚动的日志
# missingok:如果日志丢失，不报错继续滚动下一个日志
# notifempty:当日志为空时不进行滚动
# compress：旧日志默认用gzip压缩
# sharedscripts的作用是在所有的日志文件都轮转完毕后统一执行一次脚本。如果没有配置这条指令，那么每个日志文件轮转完毕后都会执行一次脚本。
# /var/run/nginx.pid：nginx主进程pid
```

测试配置
`/usr/sbin/logrotate -f /etc/logrotate.d/nginx`

Logrotate是基于CRON运行的，所以这个时间是由CRON控制的，一般默认凌晨四五点执行
我们可以通过修改crontab来修改其执行时间。
* 新版CentOS，配置文件为：/etc/anacrontab。

##### 参考
- <http://linuxcommand.org/man_pages/logrotate8.html>
- <http://huoding.com/2013/04/21/246>
