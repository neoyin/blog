---
title: Linux下搭建ShadowSocks
date: 2015-11-19 16:59
Author: Neo
categories: 技术流
tags: VPN
permalink: linux-shadow-socket-install
---


##### Linux下安装ShadowSocket

ShadowSocks 是由@clowwindy所开发的一个开源 Socks5 代理,大多用它来进行科学上网。

购买一台国外的VPS来搭建其服务 

1. 首先安装Python相关 `yum install python-setuptools && easy_install pip`
2. 安装shadowsocket `pip install shadowsocks`
3. 创建配置文件`vi /etc/shadowsocks.json`

```
{
    "server":"$your_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"XXX",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}

server  填入你的服务器 IP ，即当前操作的 VPS 的 IP 地址，必须修改
server_port 服务器端口，可以根据实际需要修改，或者保持默认
local_address   本地监听地址，建议保持默认
local_port  本地端口，这个参数一般保持默认即可
password    用来加密的密码，可以根据实际需要修改
timeout 单位秒，一般保持默认即可
method  默认的是”aes-256-cfb”，一般保持默认即可
fast_open   使用TCP_FASTOPEN, 参数选项true / false，一般保持默认即可
workers worker的数量, 在 Unix/Linux 上有效，一般不用加此项
```

4. 启动及关闭 `ssserver -c /etc/shadowsocks.json -d start|stop`

##### 客户端教程
下载shadow-socks客户端配置都很简单 不需多言.



##### 资料
- <https://wiki.archlinux.org/index.php/Shadowsocks_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)>
- <https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E>
- <http://www.auooo.com/2015/06/26/shadowsocks%EF%BC%88%E5%BD%B1%E6%A2%AD%EF%BC%89%E4%B8%8D%E5%AE%8C%E5%85%A8%E6%8C%87%E5%8D%97/>
