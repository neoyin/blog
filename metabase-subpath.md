---
title: Metabase配置子路径
date: 2021-09-06 17:10
Author: neoyin
categories: 技术流
tags:
 - metabase
 - nginx
permalink: metabase-subpath

---

#####

之前在服务器上安装了[metabase](https://www.metabase.com/) 但是需要实现子路径访问 如 `http://ip/metabase`

通过nginx 配置`proxy_path` 后并不能很好的实现，需要对一些静态资源等也同时处理



###### 解决方案

```shell
# 启动时添加系统参数 
MB_BASE_HREF=/metabase/

```

nginx中配置：

```nginx
location /metabase/ {
    proxy_pass http://localhost:3000/;
}
```



###### 参考

1. <https://github.com/metabase/metabase/pull/2393>

2. <https://stackoverflow.com/questions/1889076/what-are-the-recommendations-for-html-base-tag>