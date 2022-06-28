# 使用指定源安装私有 npm

## 需求描述

- 私有化 npm 包
- 只有私有化的 npm 包请求自己的源地址，避免流量浪费

## 方案

使用 verdaccio 建立 npm 私有化

项目中创建 .npmrc 配置文件

```
@orgName:registry=https://xxx
```

这样 `@orgName/xxx` 的都会使用指定源安装

## 发布私有化 npm

私有化的包名 `@orgName/xxx` 以这种方式命名。

发布的时候使用指定源 `npm publish --registry https://xxx`

这样就能发布到私有 npm 仓库中