---
title: Supervisor以某用户启动program
date: 2021-09-25 13:59
Author: Neo
categories: 技术流
tags:
 - linux
 - supervisor
permalink: linux-supervisord-programs-user

---



###### Supervisor 配置以某用户启动

> supervisor使用root启动，但是program 为非root 用户
>
> 添加user后不能生效，

```shell
[program:jupyter]
; 用哪个用户启动
user= jupyter

; 程序的启动目录
directory = /home/jupyter/.jupyter
; 启动命令
command = /home/jupyter/.jupyter/start.sh

; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile = /data/app_log/jupyter_stdout.log
stderr_logfile = /data/app_log/jupyter_stderr.log

```



###### 需配置environment

> <http://supervisord.org/subprocess.html#subprocess-environment>
>
> No shell is executed by supervisord when it runs a subprocess, so environment variables such as USER, PATH, HOME, SHELL, LOGNAME, etc. are not changed from their defaults or otherwise reassigned. This is particularly important to note when you are running a program from a supervisord run as root with a user= stanza in the configuration. Unlike cron, supervisord does not attempt to divine and override “fundamental” environment variables like USER, PATH, HOME, and LOGNAME when it performs a setuid to the user defined within the user= program config option. If you need to set environment variables for a particular program that might otherwise be set by a shell invocation for a particular user, you must do it explicitly within the environment= program config option. An example of setting these enviroment variables is as below.

```shell
environment=HOME="/home/jupyter",USER="jupyter"

# 示例
[program:apache2]
command=/home/chrism/bin/httpd -c "ErrorLog
/dev/stdout" -DFOREGROUND user=chrism
environment=HOME="/home/chrism",USER="chrism"

```

###### 报错 `child process was not spawned`

```shell
supervisor: couldn't exec /tmp/start_queue.sh: ENOEXEC
supervisor: child process was not spawned
### 解决 需要添加
Add #!/bin/sh at the beginning of script.
```





###### 参考

1. <https://stackoverflow.com/questions/27291448/supervisor-config-user-option-in-program-section>
2. <https://stackoverflow.com/questions/18869925/supervisord-error-child-process-was-not-spawned>

