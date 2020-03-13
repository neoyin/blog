---
title: Linux FTP上传文件
date: 2016-5-28 15:11
Author: Neo
categories: 技术流
tags:
 - Linux
 - ftp
permalink: linux-ftp-upload-file
---

----

> 一些小文件要上传到内部ftp服务器共享给其它同事 ，写一个简单的小脚本去处理。

##### 简单的示例

```
#! /bin/bash
fpath=$1  //准备上传的文件
fname=${fpath##*/}  //得到文件名 详情见</linux-string-split.html>
ftp -nv <<- EOF
open 192.168.50.199
user username password
bin
put $fname /{path}/${fname}
bye
EOF
```
- `ftp -niv <<- EOF` 由于是脚本，因此我们要给此FTP脚本定义一个结束的符号，在这里，定义的结束符是“EOF”。你也可以自己来定义其他的。不过建议用“EOF”，这在绝大多数编程语言中，都表示结束：End Of File。

```
-v：显示远程服务器的所有响应信息；
-n：限制FTP的自动登录，即不使用；
-i：关闭多个文件传输时的交互过程；
```
- `open IP_ADDRESS` 表示连接FTP站点，这里的IP_ADDRESS就是FTP站点的IP地址。
- `user USERNAME PASSWORD`  这是你进入该FTP站点的用户名和密码。以上两个结合起来，就相当于我们通过浏览器进入FTP站点时候，经常使用的`ftp://username:password@ip_address`的办法。
- `ascii(or bin)` 我们都知道FTP传送有两种方式：ascii和binary。这里就是指定你要传送的方式。这很重要，因为这将决定你传送或者获取的文件是否正常可用。
- `put *(or get)` put *表示将第一条cd进入的路径下所有的文件都传送到刚刚连接进入的FTP站点。当然，你也可以指定某一个或者几个文件，而不一定是所有的。而get则表明是将刚刚进入的FTP站点那个FTP跟目录下的文件获取到本地——第一条cd命令进入的那个路径下。当然，如果你所需要的文件不在FTP的根目录下，在使用这个命令之前，你还要使用cd命令来修改一下FTP站点的当前路径。
- `bye` 完成应该完成的工作以后，当然就是断开和FTP站点的连接了。
- `EOF` 这个符号之前已经讲过。当脚本遇到这个符号的时候，它就知道应该结束了。

##### FTP命令

```
1. 查看FTP服务器上的文件
    dir 命令：显示目录和文件列表。
    ls 命令：显示简易的文件列表。
    cd 命令：进入指定的目录。
    dir命令可以使用通配符“*”和“?”，比如，显示当前目录中所有扩展名为jpg的文件，可使用命令 dir *.jpg。
    cd命令中必须带目录名。比如 cd main 表示进入当前目录下的main子目录，cd .. 表示退回上一级子目录。

2. 传输类型
　　type 命令：查看当前的传输方式。
　　ascii 命令：设定传输方式为ASCII码方式。
　　binary 命令：设定传输方式为二进制方式。

3. 下载/上传文件
　　get 命令：下载指定文件。
　　get命令的格式：get filename [newname]，filename为下载的FTP服务器上的文件名，newname为保存在本地计算机上时使用的名字，如果不指定newname，文件将以原名保存。
　　get命令下载的文件将保存在本地计算机的工作目录下。该目录是启动FTP时在盘符C:后显示的目录。如果想修改本地计算机的工作目录，可以使用 lcd 命令。比如：lcd d:\ 表示将工作目录设定为D盘的根目录。
　　mget 命令：下载多个文件。
　　mget命令的格式：mget filename [filename ……]。
　　mget命令支持通配符“*”和“?”，比如：mget *.mp3 表示下载FTP服务器当前目录下的所有扩展名为mp3的文件。
　　put 命令：上传指定文件。
　　put命令的格式：put filename [newname]，filename为上传的本地文件名，newname为上传至FTP服务器上时使用的名字，如果不指定newname，文件将以原名上传。
　　上传文件前，应该根据文件的类型设置传输方式，本机的工作目录也应该设置为上传文件所在的目录。
   mdelete命令：批量删除文件

4. 结束命令
　　close 命令：结束与服务器的FTP会话。
　　quit 命令：结束与服务器的FTP会话并退出FTP环境。
　　bye 命令：结束与服务器的FTP会话并退出FTP环境。

5. 其它命令
　　status 命令：显示当前ftp状态
　　prompt 命令： 切换交互式指令，默认开启on/off(使用 mput/mget 时不用每个文件皆询yes/no)
　　delete 命令： 删除远端主机中的文件
```

##### 参考引用
- <http://blog.chinaunix.net/uid-20526681-id-3549245.html>



