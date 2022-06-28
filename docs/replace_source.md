# 源替换

## NPM
> 阿里：https://registry.npmmirror.com
> 
> 腾讯：http://mirrors.cloud.tencent.com/npm

```bash
$ npm config set registry https://registry.npmmirror.com

# 验证是否应用成功
$ npm get registry

https://registry.npmmirror.com
```


## Docker
> 腾讯：https://mirror.ccs.tencentyun.com

```bash
# 编辑文件
$ sudo vim /etc/docker/daemon.json

{
   "registry-mirrors": [
       "https://mirror.ccs.tencentyun.com"
  ]
}

# 重启 docker 
$ sudo service docker restart
```

## Ubuntu
> 阿里：http://mirrors.tencentyun.com/ubuntu

```bash
# 备份文件
$ sudo cp /etc/apt/sources.list /etc/apt/sources.list.back

# 编辑文件
$ sudo vim /etc/apt/sources.list

# 替换全局字符串
:%s/archive.ubuntu/mirrors.aliyun/g

# 更新源
$ sudo apt update
```
