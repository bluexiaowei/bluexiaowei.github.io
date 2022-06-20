# Docker

## sudo

```bash
# 创建用户组
$ sudo groupadd docker

# 添加用户组
$ sudo gpasswd -a ${USER} docker

# 重启 docker
$ sudo service docker restart
```

## Compose

> 最新版本 docker 默认使用 v2 版本

### V1

```bash
# 安装
$ sudo pip install -U docker-compose

# 代码补全
$ curl -L https://raw.githubusercontent.com/docker/compose/1.27.4/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose

# 卸载
$ sudo pip uninstall docker-compose
```

### V2

> github:https://github.com/docker/compose

[您可以从此存储库的发布页面下载](https://github.com/docker/compose/releases) Docker Compose 二进制文件 。

将您的操作系统的相关二进制文件重命名为 docker-compose 并将其复制到$HOME/.docker/cli-plugins

或将其复制到以下文件夹之一以在系统范围内安装它：

/usr/local/lib/docker/cli-plugins 或者/usr/local/libexec/docker/cli-plugins
/usr/lib/docker/cli-plugins 或者/usr/libexec/docker/cli-plugins
（可能需要使下载的文件可执行 chmod +x）

### FAQ

#### 两个容器之间互联 network 使用 container_name 连接

```yml
services:
  mongo:
    container_name: app-db
    image: mongo
    volumes:
      - app-mongo:/data/db
    networks:
      - app-netword
  serve:
    container_name: app-serve
    image: node:alpine3.11
    ports:
      - 3000:3000
    working_dir: /app
    command: sh -c "npm install && npm run build && npm run start:prod"
    environment:
      MONGO_URI: mongodb://app-db/pwd?authSource=admin
      PASSWORD_KEY: Hmac_SHA512
      REGISTER_PIN: 7758
      TOKEN_KEY: pwd
    volumes:
      - "./:/app"
    networks:
      - app-netword
volumes:
  app-mongo:
networks:
  app-netword:
```

## 文献

- [Docker 从入门到实践](https://github.com/yeasy/docker_practice)
