---
title: Mac下iterm2远程文件上传下载lrzsz 
date: 2017-05-20 17:34
Author: neoyin
categories: 技术流
tags: mac
permalink: mac-item2-lrzsz
---

---

Mac下iterm2 在远程机器上需要下载文件使用sz命令时不会弹出窗口下载文件

1. 安装lrzsz `brew install lrzsz`
2. 需要安装脚本到指定目录，地址: <https://github.com/mmastrac/iterm2-zmodem>
3. iterm2 添加 triggers
	`Preferences面板->Profiles选项卡->Advanced->Triggers`

```
Regular expression: \*\*B0100
    Action: Run Silent Coprocess
    Parameters: /usr/local/bin/iterm2-send-zmodem.sh
Regular expression: \*\*B00000000000000
    Action: Run Silent Coprocess
    Parameters: /usr/local/bin/iterm2-recv-zmodem.sh 
```

4. 重启iterm2，链接远程Linux，输入rz命令试一下吧

---

#### 参考
- <https://github.com/mmastrac/iterm2-zmodem> 