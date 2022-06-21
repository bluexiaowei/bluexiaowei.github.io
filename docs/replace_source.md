# 源替换

## NPM

### 临时配置

```bash
$ npm install --registry=https://registry.npmmirror.com
```

### 永久配置

```bash
$ npm config set registry https://registry.npmmirror.com
```

### nrm 控制

```bash
# 全局安装
$ sudo npm install nrm -g

# 列出所有源
$ nrm ls

  npm ---------- https://registry.npmjs.org/
  yarn --------- https://registry.yarnpkg.com/
  tencent ------ https://mirrors.cloud.tencent.com/npm/
  cnpm --------- https://r.cnpmjs.org/
  taobao ------- https://registry.npmmirror.com/
  npmMirror ---- https://skimdb.npmjs.com/registry/

# 使用淘宝源
$ nrm use taobao

   Registry has been set to: https://registry.npmmirror.com/

# 验证是否应用成功
$ npm get registry

https://registry.npmmirror.com/

```

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
