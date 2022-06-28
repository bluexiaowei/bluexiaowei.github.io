# verdaccio

> 轻量级开源私有 npm 代理注册表

## 安装

使用 `docker-compose.yml` 创建 docker 容器；

设置 nginx 代理代理配置 `npm.conf`

重启 `docker restar nginx`

## 修改配置文件

修改 verdaccio 配置，需要提升权限。

`docker exec -it -u root verdaccio sh`

```yml
# /verdaccio/conf/default.yml

uplinks:
  npmjs:
    # url: https://registry.npmjs.org/
    url: https://registry.npm.taobao.org
```

重启 `docker restar verdaccio`

## 使用

添加用户：`npm adduser --registry https://npm.u7231.top/`
用户发布：`npm publish --registry https://npm.u7231.top/`



## 文件

### `docker-compose.yml`

> 路径 verdaccio/docker-compose.yml

```yml
services:
  verdaccio:
    container_name: "verdaccio"
    image: "verdaccio/verdaccio:nightly-master"
    restart: "always"
    ports:
      - "4873:4873"
    volumes:
      - conf:/verdaccio/conf
      - storage:/verdaccio/storage
      - plugins:/verdaccio/plugins
volumes:
  conf:
  storage:
  plugins:
```

### `npm.conf`

> nginx/conf.d/npm.conf

```yml
server {
    listen 443 ssl;
    server_name  npm.u7231.top;

    ssl_certificate  cert/fullchain.pem;
    ssl_certificate_key cert/privkey.pem;

    location / {
        # 真实发出请求的客户端IP
        proxy_set_header X-Real-IP $remote_addr;
        # 记录代理信息，每经过一级代理(匿名代理除外)，代理服务器都会把这次请求的来源IP追加在X-Forwarded-For中
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       # $host变量的值按照如下优先级获得：
            # 请求行中的host.
            # 请求头中的Host头部.
            # 与一条请求匹配的server name.
        proxy_set_header Host $host;
        # 用于识别协议（HTTP 或 HTTPS），其中使用的客户端连接到代理或负载平衡器一个真实的标准报头
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass http://localhost:4873;
        # off参数取消proxy_redirect从先前配置级别继承的指令的效果
        proxy_redirect off;
    }
}
```

## 参考文献

1. [Github verdaccio](https://github.com/verdaccio/verdaccio)
