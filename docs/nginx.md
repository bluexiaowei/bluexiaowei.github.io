# Nginx

> nginx 是一款轻量级 WEB 服务器。

## 部署

### docker

**~/nginx/docker-compose.yml**

```yml
version: "2"

services:
  web:
    image: "nginx"
    container_name: "nginx"
    network_mode: "host"
    restart: always
    volumes:
      - "/root/nginx/conf.d:/etc/nginx/conf.d"
      - "/root/nginx/log:/var/log/nginx/"
      - "/root/nginx/html:/usr/share/nginx/html"
```

**~/nginx/conf.d/default.conf**

```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

**~/nginx/nginx.conf**

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```

启动命令

```bash
$ cd ~/nginx

$ docker-compose up -d
```

## 参考文献

1. [docker-compose 简介](https://vuepress.mirror.docker-practice.com/compose/introduction)
2. [CentOS 8 安装 docker/docker-compose](https://blog.csdn.net/qq_32828933/article/details/104220570)
