---
title: tar 解压指定一个文件
date: 2014-05-28 12:06
Author: chengz
categories: 每天进步一点点
tags:
 - linux
permalink: tar-unzip-file
---

    # 查看压缩包内容
     tar tf aa.tar
     tar tf aa.tar |grep file
    # 只解压一个文件 
     tar -xf aa.tar path/to/file
    # 只解压一个文件 file 到目录 abc
     tar -C abc -xf aa.tar path/to/file
