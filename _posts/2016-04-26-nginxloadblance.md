---
layout: post
title: "nginx 负载均衡 反向代理"
description: ""
category: 
tags: [nginx]
---


bling 服务器的nginx代理配置 主要有几点作用

* 负载均衡

* 反向代理

* https

```
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    proxy_read_timeout  200;
    types_hash_max_size 2048;
    gzip                on;
    gzip_min_length     1000;
    gzip_proxied        any;
    gzip_types text/plain text/html text/css text/xml
               application/x-javascript application/xml
               application/atom+xml text/javascript;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    upstream nodejs.bling.com {
        server 127.0.0.1:3000;
        #server 127.0.0.1:3001;
    }

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        client_max_body_size 10M;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_pass_header Server;
           proxy_set_header Host $http_host;
           proxy_redirect off;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Scheme $scheme;
           proxy_pass http://nodejs.bling.com;
        }

        #error_page 404 /404.html;
        #    location = /40x.html {
        #}

        #error_page 500 502 503 504 /50x.html;
        #    location = /50x.html {
        #}
    }

    server {
        listen 443;
        ssl on;
        ssl_certificate  /etc/nginx/cert/server.crt;
        ssl_certificate_key  /etc/nginx/cert/server_nopwd.key;

        server_name  _;
        client_max_body_size 10M;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass_header Server;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://nodejs.bling.com;
        }

    }
}

```



#### load balancing

```
http {
    upstream myapp1 {
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;
        }
    }
}
```











