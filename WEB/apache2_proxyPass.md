# apache2 代理转发


## 安装模块

配置代理需要加载相关模块，默认没有加载。

```bash
$ sudo a2enmod proxy
$ sudo a2enmod proxy_http
$ sudo a2enmod proxy_balancer
$ sudo a2enmod lbmethod_byrequests

$ sudo systemctl restart apache2
```

## ProxyPass 配置

进入配置文件需要 **sudo** 权限才能修改。

`$ sudo vim /etc/apach2/apach2.conf`


```
# /etc/apache2/apache2.conf
#...
ProxyPass "/api/test01/" "http://127.0.0.1:8000/"
ProxyPass "/api/test02/" "http://localhost:8001/"
```

## FAQ

### https 转发报错

apache2 配置

```
# /etc/apache2/apache2.conf
#...
ProxyPass "/api/xxxx/" "https://xxxx"
```

查看错误信息

`$ less /var/log/apach2/error.log`

```
...
[Tue Feb 23 16:03:30.014784 2021] [proxy:error] [pid 1414:tid 140447502526208] AH00961: HTTPS: failed to enable ssl support for https://xxxx
```

[解决方案](https://stackoverflow.com/questions/25107654/how-to-configure-apache-server-to-talk-to-https-backend-server)

```
# /etc/apache2/apache2.conf
#...
SSLProxyEngine on
ProxyPass "/api/xxxx/" "https://xxxx"
```
