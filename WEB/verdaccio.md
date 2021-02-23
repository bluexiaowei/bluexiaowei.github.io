# verdaccio

> 轻量级开源私有 npm 代理注册表

## 安装

### docker

```yml
# verdaccio/docker-compose.yml

version: "2"

services:
  verdaccio:
    container_name: "verdaccio"
    image: "verdaccio/verdaccio"
    restart: "always"
    ports:
      - "4873:4873"
    volumes:
      - verdaccio-conf:/verdaccio/conf
      - verdaccio-storage:/verdaccio/storage
      - verdaccio-plugins:/verdaccio/plugins
      
volumes:
  verdaccio-conf:
  verdaccio-storage:
  verdaccio-plugins:
```

## 使用

修改 verdaccio 配置，需要提升权限。

```shell
docker exec -it -u root verdaccio sh
```

```yml
# /opt/verdaccio/conf/default.yml
#...
uplinks:
    npmjs:
        # url: https://registry.npmjs.org/
        url: https://registry.npm.taobao.org
#...
```



重启 `docker restar verdaccio`



设置 nginx 反向代理。

```yml
# /etc/nginx/sites-enabled/npm

server {
  listen 80;
  listen [::]:80;
  server_name npm.xxx.com;
 
  location / {
    proxy_pass http://127.0.0.1:4873;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $remote_addr;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

检查配置 `sudo nginx -t`



重启 `sudo service nginx restart`

## 参考文献

1. [verdaccio.org](https://verdaccio.org/docs/zh-CN)