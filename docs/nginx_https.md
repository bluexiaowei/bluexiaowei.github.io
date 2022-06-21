# Nginx 配置 https 证书

使用 [certbot 工具](/docs/certbot.md)手动生成证书。

将证书 `fullchain.pem`、`privkey.pem` 移动到 `/etc/nginx/cert` 文件夹下面

在 `nginx` 配置文件中添加，并 `sudo services restart nginx` 重启 `nginx` 服务

```
server {
    listen 443 ssl;
    server_name example.com;
    ssl_certificate  cert/fullchain.pem;
    ssl_certificate_key cert/privkey.pem;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
}
```
