---
title: Linux下Supervisor进程守护监控
date: 2015-02-04 13:59
Author: Neo
categories: 技术流
tags:
 - linux
permalink: linux-supervisord
---



##### supervisord

> 运行 Supervisor 时会启动一个进程 supervisord，它负责启动所管理的进程，并将所管理的进程作为自己的子进程来启动，而且可以在所管理的进程出现崩溃时自动重启。

##### supervisorctl

> 是命令行管理工具，可以用来执行 stop、start、restart 等命令，来对这些子进程进行管理。

>  supervisor是所有进程的父进程，管理着启动的子进展，supervisor以子进程的PID来管理子进程，当子进程异常退出时supervisor可以收到相应的信号量。



#### 安装supervisord

```python
# pip安装supervisord
pip install supervisor
# 查看安装是否成功
echo_supervisord_conf
# 生成配置文件
echo_supervisord_conf > /etc/supervisord.conf
# 载入配置文件
supervisorctl  -c /etc/supervisord.conf 
```

#### 常用命令

```shell
supervisorctl stop program_name  # 停止某一个进程，program_name 为 [program:x] 里的 x
supervisorctl start program_name  # 启动某个进程
supervisorctl restart program_name  # 重启某个进程
supervisorctl stop groupworker:  # 结束所有属于名为 groupworker 这个分组的进程 (start，restart 同理)
supervisorctl stop groupworker:name1  # 结束 groupworker:name1 这个进程 (start，restart 同理)
supervisorctl stop all  # 停止全部进程，注：start、restartUnlinking stale socket /tmp/supervisor.sock
、stop 都不会载入最新的配置文件
supervisorctl reload  # 载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程
supervisorctl update  # 根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受影响而重启

```

#### echo_supervisord_conf详解：

```properties
[root@centos-011 ~ 07:50:00]#cat /etc/supervisord.conf.bak
; Sample supervisor config file.
 
[unix_http_server]
file=/var/run/supervisor/supervisor.sock   ; socket 路径
 
;chmod=0700                 ; socket 文件的权限
;chown=nobody:nogroup       ; socket 所属用户及组
;username=user              ; 用户名
;password=123               ; 密码
 
;[inet_http_server]         ; 是否启用服务，默认是关闭的（启用的话可以看到supervisor 管理的服务状态）
;port=127.0.0.1:9001        ; 监听的IP及端口
;username=user              ; 用户名
;password=123               ; 密码
 
[supervisord]               ; supervisord 全局配置
logfile=/var/log/supervisor/supervisord.log  ; supervisor 日志路径
logfile_maxbytes=50MB       ; 单个日志文件最大数
logfile_backups=10          ; 保留多少个日志文件（默认10个）
loglevel=info               ; (log level;default info; others: debug,warn,trace)
pidfile=/var/run/supervisord.pid ; pid 文件路径
nodaemon=false              ; 启动是否丢到前台，设置为false ，表示以daemon 的方式启动
minfds=1024                 ; 最小文件打开数，对应系统limit.conf 中的nofile ,默认最小为1024，最大为4096
minprocs=200                ; 最小的进程打开数，对应系统的limit.conf 中的nproc,默认为200
;umask=022                  ; (process file creation umask;default 022)
;user=chrism                 ; 启动supervisord 服务的用户，默认为root
;identifier=supervisor       ; (supervisord identifier, default is 'supervisor')
;directory=/tmp              ; 这里的目录指的是服务的工作目录
;nocleanup=true              ; (don't clean up tempfiles at start;default false)
;childlogdir=/tmp            ; ('AUTO' child log dir, default $TEMP)
;environment=KEY=value       ; (key value pairs to add to environment)
;strip_ansi=false            ; (strip ansi escape codes in logs; def. false)
 
; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
 
[supervisorctl]
serverurl=unix:///var/run/supervisor/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; should be same as http_username if set
;password=123                ; should be same as http_password if set
;prompt=mysupervisor         ; cmd line prompt (default "supervisor")
;history_file=~/.sc_history  ; use readline history if available
 
; The below sample program section shows all possible program subsection values,
; create one or more 'real' program: sections to be able to control them under
; supervisor.
 
;[program:theprogramname]      ; 定义一个守护进程 ，比如下面的elasticsearch 
;command=/bin/cat              ; 启动程序使用的命令，可以是绝对路径或者相对路径
;process_name=%(program_name)s ; 一个python字符串表达式，用来表示supervisor进程启动的这个的名称，默认值是%(program_name)s
;numprocs=1                    ; Supervisor启动这个程序的多个实例，如果numprocs>1，则process_name的表达式必须包含%(process_num)s，默认是1
;directory=/tmp                ; supervisord在生成子进程的时候会切换到该目录
;umask=022                     ; umask for process (default None)
;priority=999                  ; 权重，可以控制程序启动和关闭时的顺序，权重越低：越早启动，越晚关闭。默认值是999
;autostart=true                ; 如果设置为true，当supervisord启动的时候，进程会自动启动
;autorestart=true              ; 设置为随 supervisord 重启而重启，值可以是false、true、unexpected。false：进程不会自动重启
;startsecs=10                  ; 程序启动后等待多长时间后才认为程序启动成功，默认是10秒
;startretries=3                ; supervisord尝试启动一个程序时尝试的次数。默认是3
;exitcodes=0,2                 ; 一个预期的退出返回码，默认是0,2。
;stopsignal=QUIT               ; 当收到stop请求的时候，发送信号给程序，默认是TERM信号，也可以是 HUP, INT, QUIT, KILL, USR1, or USR2
;stopwaitsecs=10               ; 在操作系统给supervisord发送SIGCHILD信号时等待的时间
;user=chrism                   ; 如果supervisord以root运行，则会使用这个设置用户启动子程序
;redirect_stderr=true          ; 如果设置为true，进程则会把标准错误输出到supervisord后台的标准输出文件描述符
;stdout_logfile=/a/path        ; 把进程的标准输出写入文件中，如果stdout_logfile没有设置或者设置为AUTO，则supervisor会自动选择一个文件位置
;stdout_logfile_maxbytes=1MB   ; 标准输出log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小
;stdout_logfile_backups=10     ; 标准输出日志轮转备份的数量，默认是10，如果设置为0，则不备份
;stdout_capture_maxbytes=1MB   ; 当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB
;stdout_events_enabled=false   ; 如果设置为true，当进程在写它的stderr
;stderr_logfile=/a/path        ; 把进程的错误日志输出一个文件中，除非redirect_stderr参数被设置为true
;stderr_logfile_maxbytes=1MB   ; 错误log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小
;stderr_logfile_backups=10     ; 错误日志轮转备份的数量，默认是10，如果设置为0，则不备份
;stderr_capture_maxbytes=1MB   ; 当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB
;stderr_events_enabled=false   ; 如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发
;environment=A=1,B=2           ; 一个k/v对的list列表
;serverurl=AUTO                ; 是否允许子进程和内部的HTTP服务通讯，如果设置为AUTO，supervisor会自动的构造一个url
 
; The below sample eventlistener section shows all possible
; eventlistener subsection values, create one or more 'real'
; eventlistener: sections to be able to handle event notifications
; sent by supervisor.
 #这个地方是自定义一个守护进程
[program:elasticsearch]                       ; 定义一个守护进程 elasticsearch
environment=ES_HOME=/usr/local/elasticsearch  ; 设置ES_HOME 环境变量
user=elk                                      ; 启动elasticsearch 的用户
directory=/usr/local/elasticsearch            ; 进入到这个目录中
command=/usr/local/elasticsearch/bin/elasticsearch ; 执行启动命令
numprocs=1                                    ; Supervisor启动这个程序的多个实例，如果numprocs>1，则process_name的表达式必须包含%(process_num)s，默认是1
autostart=true                                ; 设置为随 supervisord 启动而启动
autorestart=true                              ; 设置为随 supervisord 重启而重启
startretries=3                                ; 设置elasticsearch 重启的重试次数
priority=1                                    ; 权重，可以控制程序启动和关闭时的顺序，权重越低：越早启动，越晚关闭。默认值是999  
 
;[eventlistener:theeventlistenername]
;command=/bin/eventlistener    ; the program (relative uses PATH, can take args)
;process_name=%(program_name)s ; process_name expr (default %(program_name)s)
;numprocs=1                    ; number of processes copies to start (def 1)
;events=EVENT                  ; event notif. types to subscribe to (req'd)
;buffer_size=10                ; event buffer queue size (default 10)
;directory=/tmp                ; directory to cwd to before exec (def no cwd)
;umask=022                     ; umask for process (default None)
;priority=-1                   ; the relative start priority (default -1)
;autostart=true                ; start at supervisord start (default: true)
;autorestart=unexpected        ; restart at unexpected quit (default: unexpected)
;startsecs=10                  ; number of secs prog must stay running (def. 1)
;startretries=3                ; max # of serial start failures (default 3)
;exitcodes=0,2                 ; 'expected' exit codes for process (default 0,2)
;stopsignal=QUIT               ; signal used to kill process (default TERM)
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (default 10)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups        ; # of stderr logfile backups (default 10)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A=1,B=2           ; process environment additions
;serverurl=AUTO                ; override serverurl computation (childutils)
 
; The below sample group section shows all possible group values,
; create one or more 'real' group: sections to create "heterogeneous"
; process groups.
 
;[group:thegroupname]          ; 服务组管理，可以将多个服务名写到这里管理(组名自定义）
;programs=progname1,progname2  ; 上面配置好的服务名，比如elasticsearch,kibana,logstash
;priority=999                  ; the relative start priority (default 999)
 
; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.
 
[include]
files = supervisord.d/*.ini
```

##### 监听程序相关配置

```properties
[program:test_one]
command=java -jar /data/smallvideo/supervisor/taskApp-exec.jar TaskTestOne  ; 被监控的进程路径
priority=1                    ; 数字越高，优先级越高
numprocs=1                    ; 启动几个进程
autostart=true                ; 随着supervisord的启动而启动
autorestart=true              ; 自动重启
startretries=10               ; 启动失败时的最多重试次数
exitcodes=0                   ; 正常退出代码
stopsignal=KILL               ; 用来杀死进程的信号
stopwaitsecs=10               ; 发送SIGKILL前的等待时间
redirect_stderr=true          ; 重定向stderr到stdout


command：启动程序使用的命令，可以是绝对路径或者相对路径
process_name：一个python字符串表达式，用来表示supervisor进程启动的这个的名称，默认值是%(program_name)s
numprocs：Supervisor启动这个程序的多个实例，如果numprocs>1，则process_name的表达式必须包含%(process_num)s，默认是1
numprocs_start：一个int偏移值，当启动实例的时候用来计算numprocs的值
priority：权重，可以控制程序启动和关闭时的顺序，权重越低：越早启动，越晚关闭。默认值是999
autostart：如果设置为true，当supervisord启动的时候，进程会自动重启。
autorestart：值可以是false、true、unexpected。false：进程不会自动重启，unexpected：当程序退出时的退出码不是exitcodes中定义的时，进程会重启，true：进程会无条件重启当退出的时候。
startsecs：程序启动后等待多长时间后才认为程序启动成功
startretries：supervisord尝试启动一个程序时尝试的次数。默认是3
exitcodes：一个预期的退出返回码，默认是0,2。
stopsignal：当收到stop请求的时候，发送信号给程序，默认是TERM信号，也可以是 HUP, INT, QUIT, KILL, USR1, or USR2。
stopwaitsecs：在操作系统给supervisord发送SIGCHILD信号时等待的时间
stopasgroup：如果设置为true，则会使supervisor发送停止信号到整个进程组
killasgroup：如果设置为true，则在给程序发送SIGKILL信号的时候，会发送到整个进程组，它的子进程也会受到影响。
user：如果supervisord以root运行，则会使用这个设置用户启动子程序
redirect_stderr：如果设置为true，进程则会把标准错误输出到supervisord后台的标准输出文件描述符。
stdout_logfile：把进程的标准输出写入文件中，如果stdout_logfile没有设置或者设置为AUTO，则supervisor会自动选择一个文件位置。
stdout_logfile_maxbytes：标准输出log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小
stdout_logfile_backups：标准输出日志轮转备份的数量，默认是10，如果设置为0，则不备份
stdout_capture_maxbytes：当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB
stdout_events_enabled：如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发
stderr_logfile：把进程的错误日志输出一个文件中，除非redirect_stderr参数被设置为true
stderr_logfile_maxbytes：错误log文件达到多少后自动进行轮转，单位是KB、MB、GB。如果设置为0则表示不限制日志文件大小
stderr_logfile_backups：错误日志轮转备份的数量，默认是10，如果设置为0，则不备份
stderr_capture_maxbytes：当进程处于stderr capture mode模式的时候，写入FIFO队列的最大bytes值，单位可以是KB、MB、GB
stderr_events_enabled：如果设置为true，当进程在写它的stderr到文件描述符的时候，PROCESS_LOG_STDERR事件会被触发
environment：一个k/v对的list列表
directory：supervisord在生成子进程的时候会切换到该目录
umask：设置进程的umask
serverurl：是否允许子进程和内部的HTTP服务通讯，如果设置为AUTO，supervisor会自动的构造一个url

```

#### 参考

- https://www.jianshu.com/p/32de18156617
- https://www.cnblogs.com/toutou/p/supervisor.html