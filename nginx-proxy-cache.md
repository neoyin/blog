---
title: nginx proxy_cache缓存
date: 2015-10-21 17:22
Author: Neoyin
categories: 技术流
tags: nginx
permalink: nginx-proxy-cache
---

-------

使用nginx的缓存功能需保证nginx添加了proxy模块。
`nginx -V` 用来查看nginx的编译参数。

```
nginx -V
nginx version: nginx/1.6.2
built by clang 7.0.0 (clang-700.0.72)
TLS SNI support enabled
configure arguments: --prefix=/usr/local/Cellar/nginx/1.6.2 --with-http_ssl_module --with-pcre --with-ipv6 --sbin-path=/usr/local/Cellar/nginx/1.6.2/bin/nginx --with-cc-opt='-I/usr/local/Cellar/pcre/8.35/include -I/usr/local/Cellar/openssl/1.0.1j/include' --with-ld-opt='-L/usr/local/Cellar/pcre/8.35/lib -L/usr/local/Cellar/openssl/1.0.1j/lib' --conf-path=/usr/local/etc/nginx/nginx.conf --pid-path=/usr/local/var/run/nginx.pid --lock-path=/usr/local/var/run/nginx.lock --http-client-body-temp-path=/usr/local/var/run/nginx/client_body_temp --http-proxy-temp-path=/usr/local/var/run/nginx/proxy_temp --http-fastcgi-temp-path=/usr/local/var/run/nginx/fastcgi_temp --http-uwsgi-temp-path=/usr/local/var/run/nginx/uwsgi_temp --http-scgi-temp-path=/usr/local/var/run/nginx/scgi_temp --http-log-path=/usr/local/var/log/nginx/access.log --error-log-path=/usr/local/var/log/nginx/error.log --with-http_gzip_static_module
```

在`nginx.conf`中配置

```
http {
...

proxy_cache_path /data/cache levels=1:2 keys_zone=my-cache:8m max_size=1000m inactive=600m;
proxy_temp_path /data/temp;
...
}
```

在server中配置

```

location ~ ^/jsp/content/view {
        proxy_pass http://127.0.0.1:8080;

        proxy_cache my-cache;
        proxy_redirect off;
        proxy_set_header        Host            $host;
        proxy_set_header        X-Real-IP       $remote_addr;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        #add_header X-Cache HIT;
        proxy_cache_valid  200 304 301 302 10d;
        proxy_cache_valid  any 1d;
        proxy_cache_key $host$uri$is_args$args;
}
```

重启nginx生效

#### 参考
- <http://www.nginx.cn/414.html>
- <http://blog.csdn.net/liujiyong7/article/details/18228915>
- <http://www.ha97.com/5194.html>
- <http://www.nosa.me/2014/10/31/nginx-cache%E6%96%B9%E9%9D%A2%E7%9A%84%E8%AE%BE%E7%BD%AE/>
- <http://www.firefoxbug.com/index.php/archives/2089/>
