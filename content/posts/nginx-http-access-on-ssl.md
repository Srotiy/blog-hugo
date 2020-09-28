---
title: "Nginx 配置特定目录使用http访问"
date: 2018-03-22T11:17:18+08:00
draft: false
---
```nginx
    server {
        server_name www.srotiy.com srotiy.com;

        location ^~ /test/ {
            alias /www/wwwroot/srotiy.com/test/;
            try_files $uri =404;
        }

        location / {
            rewrite ^/(.*)$ https://srotiy.com/$1 permanent;
        }
    }
```

以上配置优先查找 ~/www/wwwroot/srotiy.com/test 目录下的文件，如果找不到就重定向到 HTTPS 地址。 至于用处嘛，可以在证书挂掉时用来验证域名所有权。