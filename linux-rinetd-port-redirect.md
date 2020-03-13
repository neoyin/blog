---
title: Linux下利用rinetd进行端口转发
date: 2016-12-22 15:34
Author: neoyin
categories: 技术流
tags:
 - rinetd
 - linux
permalink: linux-rinetd-port-redirect
---

---

最近用使用Aliyun数据库时需要使用公网直接连接数据库,发现其文档中是使用了`rinetd`，虽然之前一直知道`rinetd`，却未用过，现在给记录下来.

##### 相关地址

- <https://boutell.com/rinetd/> 

##### 下载安装

```
wget http://www.boutell.com/rinetd/http/rinetd.tar.gz
tar -xvf rinetd.tar.gz&&cd rinetd
#(修改端口范围)
sed -i 's/65536/65535/g' rinetd.c 
mkdir /usr/man&&make&&make install
```

##### 配置

```
vim /etc/rinetd.conf
#将所有发往本机的8080端口的请求转发到192.168.1.x的80端口
0.0.0.0 8080 192.168.1.x 80
logfile /var/log/rinetd.log
#格式说明
#bindaddress bindport connectaddress connectport
#绑定的地址  绑定的端口  转发的地址  转发的端口
```

##### 启动及关闭

```
pkill rinetd  ##关闭进程
rinetd -c /etc/rinetd.conf  ##启动转发
echo rinetd >>/etc/rc.local里面就可以开机自动运行
```

---

##### 参考

- <https://help.aliyun.com/knowledge_detail/39952.html>
- <https://boutell.com/rinetd/>