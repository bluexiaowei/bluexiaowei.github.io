# 源替换

## Ubuntu

- `sudo cp /etc/apt/sources.list /etc/apt/sources.list.back` 备份文件
- `sudo vim /etc/apt/sources.list` 编辑文件
- `:%s/archive.ubuntu/mirrors.aliyun/g` 替换全局字符串
- `sudo apt update` 更新源
