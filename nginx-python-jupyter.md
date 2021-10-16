---
title: Nginx配置Jupyter子路径
date: 2021-09-04 17:10
Author: neoyin
categories: 技术流
tags:
 - python
 - jupyter
 - nginx
permalink: nginx-python-jupyter

---

#####Jupter 相关配置

```python
# ~/.jupyter/jupyter_notebook_config.py
c.NotebookApp.port = 8878
c.NotebookApp.base_url = '/groot'
# 去除jupter密码设置
c.NotebookApp.password_required = False
c.NotebookApp.token = ''
c.NotebookApp.password = u''

```

##### Nginx相关配置

添加相关配置

```python
 # 添加一个upstream
  upstream upstream_groot {
    server localhost:8888;
    keepalive 32;
  }
  
  location = /groot {
      rewrite ^/(.*)$ $1/ permanent;
    }
    location /groot {
      error_page 403 = @proxy_groot;
      deny 127.0.0.1;
      allow all;
      # set a webroot, if there is one
      root /some-webroot;
      try_files $uri @proxy_groot;
    }
    location @proxy_groot {
      #rewrite /groot(.*) $1  break;
      proxy_read_timeout 300s;
      proxy_pass http://upstream_groot;
      # pass some extra stuff to the backend
      proxy_set_header Host $host;
      proxy_set_header X-Real-Ip $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    location ~ /groot/api/kernels/ {
            proxy_pass            http://upstream_groot;
            proxy_set_header      Host $host;
            # websocket support
            proxy_http_version    1.1;
            proxy_set_header      Upgrade "websocket";
            proxy_set_header      Connection "Upgrade";
            proxy_read_timeout    86400;
        }
    location ~ /groot/terminals/ {
            proxy_pass            http://upstream_groot;
            proxy_set_header      Host $host;
            # websocket support
            proxy_http_version    1.1;
            proxy_set_header      Upgrade "websocket";
            proxy_set_header      Connection "Upgrade";
            proxy_read_timeout    86400;
    }
```



##### 参考

1. <https://www.nathantsoi.com/blog/run-jupyter-notebook-behind-a-nginx-reverse-proxy-subpath/index.html>

2. <https://www.pianshen.com/article/1434301772/>

