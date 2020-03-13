---
title:  Intellij Ideal 快捷键
date: 2015-12-27 13:29
Author: Neo
categories: 每天进步一点点
tags:
 - idea
permalink: intellij-idea-shortcuts
---


用了一阵子的Intellij Idea 结果发现Eclipse的快捷键快忘了。备个份.

##### 一些技巧：
> 在任何时候，按下alt键，界面上的菜单字符中有些会有下划线，这时用alt+下划线即是执行这个菜单功能的快捷键。可自定义一些快捷键，避免使用鼠标。IDEA支持宏，不过我用得不多，只用过那么一两次，使用起来也很简单。

##### 视图切换快捷键

1. 各视图区域的切换 cmd + 视图区域对应的数字
2. 切换到maven的视图 alt + m
3. 装了switchTab插件后，按下ctrl+tab后会得到可切换的区域列表
4. cmd+e  列出最近查看的文件列表
5. shift + cmd + e   最近修改文件列表

##### 搜索快捷键

1. 在编辑文档中，cmd+f 开始搜索 cmd + r 搜索替换
2. 在project中，按下ctrl + shift + f(r) 即是在当前目前下递归查找或替换,搜索出来后，要全部替换，按下alt + a
3. alt + F7      搜索对象被引用的地方
4. cmd + F7   搜索对象在当前文件被引用的地方
5. cmd + n     查找类
6. shift + cmd + n  查找文件
> 上面两个查找都支持简单的正则表达式，还支持直接按大写字母的缩略查找如JsonTranscoder,只需要输入JT

##### 编辑代码的快捷键

1. cmd + j                 调出IDEA的神器live template，你懂的
2. ctrl + n                各种自动生成代码，创建文件，生成set get override…
3. alt + enter           调出IDEA对出错点的提示处理方法，熟练使用可使你写代码的速度提升5倍
4. cmd + D              复制粘贴当前行到下一行
5. cmd + c|v|x        这些你都懂的
6. shift + cmd + v  调出IDEA寄存器中保存的最近复制的N份内容，可选择性粘贴
7. cmd + /                注释/取消注释
8. cmd + w               选中当前光标所在的字
9. shift + cmd + enter 神器，补全当前行，最常用的场景时补全当前行后的；号，并将光标定位到下一行
10. cmd + O              overide 方法
11. cmd + I                implement 方法
其实上面两个快捷键很少用，因为有alt + enter 可完全替换掉这两个快捷键的功能
12. shift + cmd + L                   格式化代码
13. shift + cmd + O                   static import
14. shift + cmd + up/down    将当前代码段上/下移
15. shift + alt + up/down       将当前行上/下移

##### 重构的快捷键

1. F6 移动文件到其它地方
2. F5 拷贝文件到其它地方
3. IDEA的移动文件功能非常强大，会将所有的使用该文件的类和XML配置中的类路径全部更新为新的路径。
4. shift + F6              改名
5. cmd + F6                修改方法签名
6. shift + cmd + F6    修改参数的类型
7. shift + cmd + v       引入一个局部变量
8. shift + cmd + p       引入一个参数
9. shift + cmd + f        引入一个类变量
10. shift + cmd + m      引入一个方法
11. shift + cmd + c        引入一个常量
12. alt + cmd + F7      查看某方法被调用的地方

##### 查看代码快捷键

1. cmd + p                  查询某方法的参数信息
2. cmd + b                  跳到调用方法的定义处
3. shift + cmd + b          跳到方法的实现处
4. cmd + u                  跳到方法在父类或接口的定义处
5. ctrl + up/down           跳到上/下一方法
6. alt + cmd + left/right   跳到上/下一次光标查看处
7. cmd + g                  跳到指定行
8. cmd + shift + h          显示方法的层级结构
9. alt + cmd + h            调用层级结构

##### 运行程序与Debug

1. shift + alt + F9                      debug模式运行程序
2. shift + alt + F10                     运行程序
3. F7                                    单步进入
4. F8                                    单步跳过
5. F9                                    跳过本次debug
6. alt + F8                              debug时执行选中的语句

##### 其它常用快捷键

1. cmd + ,                  调出setting界面
2. cmd + ;                  调出项目setting界面
3. cmd + f4                 关闭当前界面

##### 其它设置


1. 显示行号 
如何显示行号：Settings->Editor->Appearance标签项，勾选Show line numbers
2. 如何让光标不随意定位 
Settings->Editor中去掉Allow placement of caret after end of line。
3. 中文乱码问题
File菜单->Settings->Colors & Fonts->Editor Font=宋体, size=12, line spacing =1.0
Settings->Appearance中勾选Override default fonts by (not recommended)，设置Name:NSimSun，Size:12

![Intellij Idea Eclipse 快捷键](http://e.picphotos.baidu.com/album/s%3D550%3Bq%3D90%3Bc%3Dxiangce%2C100%2C100/sign=638ef1ed0d46f21fcd345e56c61f1a5d/7acb0a46f21fbe09df277c026c600c338744adaf.jpg?referer=b8931b0590dda144831e59820b65&x=.jpg)

<https://www.jetbrains.com/idea/docs/IntelliJIDEA_ReferenceCard_Mac.pdf>
<http://blog.csdn.net/zhangt85/article/details/43670359>
