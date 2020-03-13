---
title: Python安装pip 
date: 2016-10-26 12:10
Author: neoyin
categories: 技术流
tags:
 - python
permalink: python-install-pip
---

---

之前调用Rancher API的python脚本需要使用pip 默认python并没有安装pip命令

##### Pip安装

- 使用软件管理器安装

Debian 和 Ubuntu:下安装
`sudo apt-get install python-pip`
 
由于Mac下没有相关命令brew也没有pip的安装 可以用源码安装 

```
git clone https://github.com/pypa/pip
cd pip
sudo python setup.py install
```

验证安装成功
```
pip -V
pip 1.6.dev1 from /Library/Python/2.7/site-packages/pip-1.6.dev1-py2.7.egg (python 2.7)
```

 ---

##### 参考
- <http://stackoverflow.com/questions/18358878/cant-find-pip-executable-on-mac-os-x>
- <http://pip-cn.readthedocs.io/en/latest/installing.html>