---
title: Linux shell命令字符串操作
date: 2016-5-27 15:11
Author: Neo
categories: 技术流
tags:
 - Linux
permalink: linux-string-split
---

----

##### 字符串操作（长度，读取，替换）

```
${#string}	$string的长度 
${string:position}	在$string中, 从位置$position开始提取子串
${string:position:length}	在$string中, 从位置$position开始提取长度为$length的子串	 
${string#substring}	从变量$string的开头, 删除最短匹配$substring的子串
${string##substring}	从变量$string的开头, 删除最长匹配$substring的子串
${string%substring}	从变量$string的结尾, 删除最短匹配$substring的子串
${string%%substring}	从变量$string的结尾, 删除最长匹配$substring的子串	 
${string/substring/replacement}	使用$replacement, 来代替第一个匹配的$substring
${string//substring/replacement}	使用$replacement, 代替所有匹配的$substring
${string/#substring/replacement}	如果$string的前缀匹配$substring, 那么就用$replacement来代替匹配到的$substring
${string/%substring/replacement}	如果$string的后缀匹配$substring, 那么就用$replacement来代替匹配到的$substring
```
1. 长度

```
[~]$ test='I love china'
[~]$ echo ${#test}
12
${#变量名}得到字符串长度
```
2. 截取字串

```
[~]$ test='I love china'
[~]$ echo ${test:5}     
e china
[~]$ echo ${test:5:10} 
e china
${变量名:起始:长度}得到子字符串
```
3. 字符串删除

```
[~]$ test='c:/windows/boot.ini'
[~]$ echo ${test#/}
c:/windows/boot.ini
[~]$ echo ${test#*/}
windows/boot.ini
[~]$ echo ${test##*/}
boot.ini
[~]$ echo ${test%/*} 
c:/windows
[~]$ echo ${test%%/*}
${变量名#substring正则表达式}从字符串开头开始配备substring,删除匹配上的表达式。
${变量名%substring正则表达式}从字符串结尾开始配备substring,删除匹配上的表达式。
注意：${test##*/},${test%/*} 分别是得到文件名，或者目录地址最简单方法。
```
4. 字符串替换

```
[~]$ test='c:/windows/boot.ini'
[~]$ echo ${test/\//\\}
c:\windows/boot.ini
[~]$ echo ${test//\//\\}
c:\windows\boot.ini
${变量/查找/替换值} 一个“/”表示替换第一个，”//”表示替换所有,当查找中出现了：”/”请加转义符”\/”表示。
```

