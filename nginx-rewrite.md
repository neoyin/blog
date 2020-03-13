---
title: nginx Rewrite
date: 2013-10-19 17:22
Author: chengz
categories: 技术流
tags: nginx
permalink: nginx-rewrite
---

###### `query string`

```
if ($query_string ~ 'postId=([0-9]+).*FirstTimeFor' ) {
                set $board_id $1;
            rewrite /view.action  http://xxx.yyy.com/topic/single.shtml?dashboard_id=$board_id? permanent;
        }

rewrite /view.action?postId=([0-9]+)&sourceFlag=false&client=key:FirstTimeForiPhone$ http://firsttime.l99.com/topic/single.shtml?dashboard_id=$1 last;
```

###### `static proxy pass`

```
location ~ ^/(css|present|pin|avatarsRes|commentImg|flash|images|js|skin)/ {
        proxy_pass http://static.l99.com;
}
```

###### `number`
```
rewrite ^/([0-9]+)/photo/album/([0-9]+)/p([0-9]+)$ /EditPicture_albumPhoto.action?albumId=$2&nowPage=$3 last;
```

###### `if`
```
        set $flag 0;
        if ($http_user_agent ~ iPhone) {
            #rewrite ^/\?p=([0-9]+)$ http://api.pintimes.com/jsp/content/singleview/$1? permanent;
            set $flag "${flag}1";
        }
        set $postId 0;
        if ($query_string ~ 'p=([0-9]+)$') {
            set $flag "${flag}2";
            set $postId $1;
        }

        if ($flag = "012") {
            rewrite (.*) http://xxx.yyy.com/jsp/content/singleview/$postId? permanent;
        }
```
