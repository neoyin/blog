---
title: Mac升级python
date: 2017-04-15 17:34
Author: neoyin
categories: 技术流
tags:
 - python
permalink: mac-update-python
---

---

最近研究一下`TensorFlow` 想升级一下mac下的python 

网上找到脚本如下

```shell
#!/bin/bash
#python版号需要修改两个地方
#1. new_version
#sudo -i #得到超级权限
new_version="3.6"
PYPATH=/System/Library/Frameworks/Python.framework/Versions/"$new_version"
#第1步移动新版python到mac默认目录下
echo "move.."
mv /Library/Frameworks/Python.framework/Versions/"$new_version" /System/Library/Frameworks/Python.framework/Versions/
#第2步改变用户目录的用户组
echo "chown.."
chown -R neoyin:admin ${PYPATH}
#第3步 删除原来2.7的链接
echo "del.."
rm /System/Library/Frameworks/Python.framework/Versions/Current
#第4步重新链接到最新版本的python
echo "ln.."
ln -s ${PYPATH} /System/Library/Frameworks/Python.framework/Versions/Current
#第5步删除旧的命令符号链接
echo "rm.."
rm /usr/bin/{pydoc,python,pythonw,python-config}
echo "ln bin.."
#第6步重新建立新的命令符号链接
ln -s ${PYPATH}/bin/pydoc"$new_version" /usr/bin/pydoc
ln -s ${PYPATH}/bin/python"$new_version" /usr/bin/python
ln -s ${PYPATH}/bin/pythonw"$new_version" /usr/bin/pythonw
ln -s ${PYPATH}/bin/python"$new_version"m-config /usr/bin/python-config
python_param_list=`cd /usr/local/bin && ls -al |grep "Python"|awk 'ORS=" " {print $9}'`
#第7步修复其他链接
for i in $python_param_list;do
  echo "info: $i"
  rm -f /usr/local/bin/${i}
  ln -sv /System/Library/Frameworks/Python.framework/Versions/"$new_version"/bin/${i} /usr/local/bin/${i}
done
#第8步.环境变量要修改为最新的版本号
echo 'export PATH=/System/Library/Frameworks/Python.framework/Versions/3.4/bin:${PATH}' >> ~/.bashrc
exit #退出超级权限
```

mac下`/System/Library/Frameworks`目录没有权限 
解决方法: 
重启电脑,长按Command+r 进入Recovery模式
打开终端 
执行如下命令:
`csrutil disable`
重启即可.
