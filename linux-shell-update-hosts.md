---
title: Linux修改hosts脚本
date: 2015-12-16 09:27
categories: 技术流
tags:
 - Linux
permalink: linux-shell-update-hosts
---

由于项目测试经常需要修改hosts文件 将域名定向到内部服务器，所以写了一个简单的小脚本如下:

```
hostname="$2"
hostip="$1"
Hosts="/etc/hosts"

if [[ -n $hostname ]] && [[ -n $hostip ]]
then
   echo "delete line " $hostname
   #如果之前已存在 则删除
   #Mac 下小问题见下参考 
   #sed -i '' '/'${hostip}'.*'${hostname}'*$/d' $Hosts
   sed -ie '/'${hostip}'.*'${hostname}'*$/d' $Hosts
   # addhostname为个人所设置关键字 将添加的hostname插入到此行之后
   #Mac 下sed插入需要\
   sed -ie '/addhostname/ a\
   '${hostip}' '${hostname}'
   ' $Hosts
fi

```

将此脚本放入`$PATH`中或者使用来操作alias
`sudo addhosts www.test.com 192.168.1.199`


##### 参考
- <http://www.mkyong.com/mac/sed-command-hits-undefined-label-error-on-mac-os-x/>
- <https://discussions.apple.com/thread/2154145?tstart=0>
- <http://pulipuli.blogspot.com/2015/05/ubuntuetchosts-how-to-write-script-for.html>
