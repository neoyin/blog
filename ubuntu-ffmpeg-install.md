---
title: Ubuntu下安装FFmpeg
date: 2016-9-7 15:11
Author: Neo
categories: 技术流
tags: Linux,ffmpeg,android
permalink: ubuntu-ffmpeg-install
---

---

最近遇到一个问题：关于Android下Video快进功能
利用mediaplay 的`seekTo()`不能精确定位，一些资料提到跟视频的关键帧有关系 决定利用FFmpeg试试看. 

网上找了个安装脚本如下:

```
sudo
 apt-get install build-essential git-core checkinstall yasm texi2html 
libvorbis-dev libx11-dev libvpx-dev libxfixes-dev zlib1g-dev pkg-config 
netcat libncurses5-dev
#version 
FFMPEG_VERSION=3.1.3
cd /usr/local/src
if [ ! -d "/usr/local/src/ffmpeg-${FFMPEG_VERSION}" ]; then
  sudo wget "http://ffmpeg.org/releases/ffmpeg-${FFMPEG_VERSION}.tar.bz2"
  sudo tar -xjf "ffmpeg-${FFMPEG_VERSION}.tar.bz2"
fi
cd "ffmpeg-${FFMPEG_VERSION}"
sudo ./configure --enable-version3 --enable-postproc --enable-libvorbis --enable-libvpx
sudo make
sudo checkinstall --pkgname=ffmpeg --pkgversion="5:${FFMPEG_VERSION}" --backup=no --deldoc=yes --default
```
经过一通编译处理终于安装成功了

```
ffmpeg -version
ffmpeg version 3.1.3 Copyright (c) 2000-2016 the FFmpeg developers
built with gcc 4.8 (Ubuntu 4.8.5-2ubuntu1~14.04.1)
configuration: --enable-version3 --enable-postproc --enable-libvorbis --enable-libvpx
libavutil      55. 28.100 / 55. 28.100
libavcodec     57. 48.101 / 57. 48.101
libavformat    57. 41.100 / 57. 41.100
libavdevice    57.  0.101 / 57.  0.101
libavfilter     6. 47.100 /  6. 47.100
libswscale      4.  1.100 /  4.  1.100
libswresample   2.  1.100 /  2.  1.100
```

---

- <http://www.cnblogs.com/war-hzl/p/5393308.html>
- <http://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu>
- <http://bbs.csdn.net/topics/390852321>