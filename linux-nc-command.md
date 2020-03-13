---
title: Linux最实用命令nc
date: 2016-9-5 15:11
Author: Neo
categories: 技术流
tags:
 - Linux
 - nc
permalink: linux-nc-command
---

---

我的两台电脑要简单的传输一条命令，突然想起了nc命令可以监听端口从而实现两台电脑可以简简单单的聊天功能，也特地把nc命令备忘起来。 

```
nc -lp 8888  #实现简单的监听
nc {ip} 8888 即可打通一个链接  
```
指令如下:

```
语　法：nc [-hlnruz][-g<网关...>][-G<指向器数目>][-i<延迟秒数>][-o<输出文件>][-p<通信端口>][-s<来源位址>][-v...][-w<超时秒数>][主机名称][通信端口...]
参　数：
 -g<网关> 设置路由器跃程通信网关，最丢哦可设置8个。
 -G<指向器数目> 设置来源路由指向器，其数值为4的倍数。
 -h  在线帮助。
 -i<延迟秒数> 设置时间间隔，以便传送信息及扫描通信端口。
 -l  使用监听模式，管控传入的资料。
 -n  直接使用IP地址，而不通过域名服务器。
 -o<输出文件> 指定文件名称，把往来传输的数据以16进制字码倾倒成该文件保存。
 -p<通信端口> 设置本地主机使用的通信端口。
 -r  乱数指定本地与远端主机的通信端口。
 -s<来源位址> 设置本地主机送出数据包的IP地址。
 -u  使用UDP传输协议。
 -v 详细输出--用两个-v可得到更详细的内容
 -w<超时秒数> 设置等待连线的时间。
  -z  使用0输入/输出模式，只在扫描通信端口时使用。
```
也可以进行文件传输

```
root@10:~# nc -l -p 1234 > test.txt        #开10.1.1.180:1234端口监听，并将socket传输过来的数据重定向到test.txt文件 test 43 nc
root@10.1.1.43:~#cat test.txt    
root@10.1.1.43:~# nc  10.1.1.180 1234 < test.txt    #连接远程的10.1.1.180，从test.txt的路径从定向到socket，从而将文件传输到远方。
root@10:~# cat test.txt
test 43 nc
```

目录传输
```
root@10:~# nc -l -p 1234 | tar xzvf -
root@10.1.1.43:~# tar czvf -  python_program | nc 10.1.1.180 1234
python_program/
python_program/1.py
python_program/4.py
python_program/3.py
```

---

- <http://netcat.sourceforge.net/>