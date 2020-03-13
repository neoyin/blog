---
title: Linux添加Swap文件
date: 2015-12-07 15:52
Author: neoyin
categories: 技术流
tags: Linux
permalink: linux-swapon
---


上次在aws上创建了一服务器，但是用free命令查看时发现swap为0,决定添加上交换文件

```
             total       used       free     shared    buffers     cached
Mem:       1019452     419436     600016        104      14824      96564
-/+ buffers/cache:     308048     711404
Swap:            0          0          0
```

首先登陆服务器并切换成root用户
```

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2015.09-release-notes/
 su - //su -s
```

1. 利用dd命令创建存储文件,这里我们创建一个512M的文件足矣 
```
# dd if=/dev/zero of=/swapfile1 bs=1024 count=524288
524288+0 records in
524288+0 records out
536870912 bytes (537 MB) copied, 3.23347 s, 166 MB/s

> if=/dev/zero : Read from /dev/zero file. /dev/zero is a special file in that provides as many null characters to build storage file called /swapfile1.
> of=/swapfile1 : Read from /dev/zero write storage file to /swapfile1.
> bs=1024 : Read and write 1024 BYTES bytes at a time.
> count=524288 : Copy only 523288 BLOCKS input blocks.

```

2. 分配swapfile1权限
```
# chown root:root /swapfile1
# chmod 0600 /swapfile1
```

3. 建立一个交换区
```
# mkswap /swapfile1

> Setting up swapspace version 1, size = 524284 KiB
> no label, UUID=0e5e7c60-bbba-4089-a76c-2bb29c0f0839
```

4. 开启交换文件
```
# swapon /swapfile1
```

5. 启动时挂载
```
# vi /etc/fstab
/swapfile1 none swap sw 0 0
```

再来free看一看
```
             total       used       free     shared    buffers     cached
Mem:       1019452     419436     600016        104      14824      96564
-/+ buffers/cache:     308048     711404
Swap:       524284          0     524284
```


##### 参考
- <http://www.cyberciti.biz/faq/linux-add-a-swap-file-howto/>