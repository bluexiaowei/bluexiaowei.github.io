# 源替换

## Ubuntu

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
