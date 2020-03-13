---
title: 设计师项目Nodejs前端项目
date: 2017-01-03 16:34
Author: neoyin
categories: 技术流
tags:
 - docker
 - springboot
 - react-native
 - nodejs
permalink: the-designer-mobile-nodejs
---

---

##### Sails

`rest`API的工作准备完成，接下来就是前端了,这里没有想做Web端，直接上mobile版本. 

选择`nodejs` 是应该之前注意到一些比较不错的`nodejs`框架，而且如果后期有前端人员接手能更好的展开工作. 

这里选择了<http://sailsjs.com/> 
```
npm install sails -g
sails new test-project
```
项目结构可以看这里<http://sailsjs.com/documentation/anatomy/>

sails使用了`ejs`模板引擎<http://www.ejs.co/>
前端使用了AUI框架<http://www.auicss.com/>

完成之后直接在项目目录下运行
`node app.js --prod`

前端感觉没有什么特别要讲了，主要讲一下使用到了哪些框架。 看一看文档
根据文档完成自己的项目逻辑很快就能开发出一个前端项目了. 

<https://designer.L99.com> 基本花了4，5天完成。主要是调页面样式花时间，毕竟本职不是前端。


