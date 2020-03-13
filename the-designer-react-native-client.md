---
title: 设计师项目react-native客户端项目
date: 2017-01-03 17:34
Author: neoyin
categories: 技术流
tags: docker,springboot,react-native
permalink: the-designer-react-native-client
---

---

之前完成了nodejs的mobile版本<https://designer.L99.com> 说明项目已经有用户可以看见，且相关分享及浏览有了落地。

接下来就是客户端了, 这里毫不意外的使用了`react-native`  这里用不着讲体会和优化啥的，官网上的介绍已经很详细了<http://reactnative.cn/docs/0.40/getting-started.html>，还没到需要的时候，主要写一下使用到了哪些dependencies和遇到了哪些问题

目前官网上的版本是0.4.0 项目使用的是0.3.8版本。 

###### 个人准备
- 详读一下文档，如上地址有中文版<http://reactnative.cn/docs/0.40/getting-started.html>
- 仔细熟悉一下ES6 <http://es6.ruanyifeng.com> 阮一峰老师的
- 自己之前CSS及JS知识的积累
- 自己之前Android知识的积累 

###### 项目使用组件
- 头部导航组件 <https://github.com/exponent/react-native-tab-navigator>
- 尾部选项卡 <https://github.com/react-native-community/react-native-navbar>
- 开机介绍 <https://github.com/FuYaoDe/react-native-app-intro>
- 设备信息 <https://github.com/rebeccahughes/react-native-device-info>
- 微信登陆分享 <https://github.com/reactnativecn/react-native-wx>

##### Flex布局
> 阮一峰老师的两篇文章可以看一下

- <http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html>
- <http://www.ruanyifeng.com/blog/2015/07/flex-examples.html>

##### 遇到问题

- Webview 高度问题<https://github.com/danrigsby/react-native-web-container>
- 瀑布流 
> react-native 没有支持如`column`的样式，用flex也不是太好实现，要写原生的也没有这水平，也没有看到相关的组件，所以用`ScrollView`和N个垂直的View，子元素加入到这几个View中。



