---
title: Linux 命令lsof
date: 2017-07-30 09:27
categories: 技术流
tags: Linux,lsof
permalink: linux-command-lsof
---

---

之前应该有写过关于`lsof`命令文章，但是感觉有些生疏了,抄一篇笔记熟悉一下.

#### 关键选项
> 理解一些关于lsof如何工作的关键性东西是很重要的。最重要的是，当你给它传递选项时，默认行为是对结果进行“或”运算。因此，如果你正是用-i来拉出一个端口列表，同时又用-p来拉出一个进程列表，那么默认情况下你会获得两者的结果。

```
	•	默认 : 没有选项，lsof列出活跃进程的所有打开文件
	•	组合 : 可以将选项组合到一起，如-abc，但要当心哪些选项需要参数
	•	-a : 结果进行“与”运算（而不是“或”）
	•	-l : 在输出显示用户ID而不是用户名
	•	-h : 获得帮助
	•	-t : 仅获取进程ID
	•	-U : 获取UNIX套接口地址
	•	-F : 格式化输出结果，用于其它命令。可以通过多种方式格式化，如-F pcfn（用于进程id、命令名、文件描述符、文件名，并以空终止）
```



#### 获取网络信息

```
# lsof -i  显示所有连接
 
COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
dhcpcd 6061 root 4u IPv4 4510 UDP *:bootpc
sshd 7703 root 3u IPv6  6499 TCP *:ssh (LISTEN)
sshd 7892 root 3u IPv6  6757 TCP 10.10.1.5:ssh->192.168.1.5:49901 (ESTABLISHED)

# lsof -i 6 仅获取IPv6流量
WeChat    71196 neoyin  113u  IPv6 0x8c1bef96d552c045      0t0  TCP 192.168.99.167:64892->182.254.89.122:http-alt (ESTABLISHED)

# lsof -iTCP 仅显示TCP连接（同理可获得UDP连接）

# lsof -i :22  使用-i:port来显示与指定端口相关的网络信息

# lsof -i@192.168.99.205 使用@host来显示指定到指定主机的连接

# lsof -i@192.168.99.205:22 使用@host:port显示基于主机与端口的连接

# lsof -i -sTCP:LISTEN 找出正等候连接的端口
# lsof -i | grep -i LISTEN

# lsof -i -sTCP:ESTABLISHED 显示任何已经连接的连接
# lsof -i | grep -i ESTABLISHED

```



#### 用户信息

```
# lsof -u neoyin  使用-u显示指定用户打开了什么

# lsof -u ^neoyin 显示除指定用户以外的其它所有用户所做的事情

```



#### 命令和进程

```
# lsof -c syslog-ng  使用-c查看指定的命令正在使用的文件和网络连接

# lsof -p 10075  使用-p查看指定进程ID已打开的内容

# lsof -t -c java -t选项只返回PID 
```



#### 文件和目录

```
# lsof /var/log/nginx/ 显示与指定目录交互的所有一切

# lsof /var/log/install.log 显示与指定文件交互的所有一切
```



---

##### 参考

- <http://www.netadmintools.com/html/lsof.man.html>

- <https://linux.cn/article-4099-1.html>

  





