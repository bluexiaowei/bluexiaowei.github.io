# 更换国内源

```bash
go env -w GOPROXY=https://goproxy.cn
```

## 安装依赖

```bash
go mod init test

go get -u github.com/gin-gonic/gin
```