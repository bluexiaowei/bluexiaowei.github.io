# Frp

> frp 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。

## 部署

### Docker

**~/frp/docker-compose.yml**

```yml
version: "2"
services:
  frp:
    image: "snowdreamtech/frps"
    container_name: "frps"
    network_mode: "host"
    restart: always
    volumes:
      - "/root/frp/frps.ini:/etc/frp/frps.ini"
```

**~/frp/frps.ini**

```
[common]
bind_port = 10000
vhost_http_port = 10001
vhost_https_port = 10002
dashboard_addr = 0.0.0.0
dashboard_port = 10003
dashboard_user = admin
dashboard_pwd = AwZejv6C6JKM4ddD
token = nNZdflvUF0LYD7pM
```

## 参考文献

1. [frp Github](https://github.com/fatedier/frp)
2. [frp docker image](https://github.com/snowdreamtech/frp)
