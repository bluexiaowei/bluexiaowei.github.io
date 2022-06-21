# Windows 10

## 软件

- [Winget](https://github.com/microsoft/winget-cli) 包管理工具
- [Windows Terminal](https://github.com/microsoft/terminal) 漂亮的终端
- [Motrix](https://github.com/agalwood/Motrix) 一款全能的下载工具
- [V2rayN](https://github.com/2dust/v2rayN) v2ray 客户端
- [Koodo Reader](https://github.com/troyeguo/koodo-reader) 电子书阅读器
- [VSCode](https://github.com/microsoft/vscode) 代码编辑器
- [WSL](https://docs.microsoft.com/zh-cn/windows/wsl) windows 子系统

### WSL

#### 前置条件

> 控制面板>程序>开启或关闭 Windows 功能

- windows 10 专业版
- 开启 Linux 的 Windows 子系统
- 开启 Hyper-v （ WSL2 需要开启）

#### 安装

- `wsl --install` 安装 wsl
- [适用于 x64 计算机的 WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) 点击下载并安装
- `wsl --set-default-version 2` 设置默认版本
- `wsl --update`更新 wsl
- `wsl --install -d Ubuntu`安装 Ubuntu 发行版

#### 问题

##### Windows 添加 WSL 快捷访问路径

> 打开文件资源管理器在“此电脑”右击，选择“映射网络驱动器”添加文件夹路径“\\wsl$\Ubuntu”

##### Docker 无法 `sudo apt install docker.io`无法正常使用

```bash
$ sudo apt install docker.io -y
$ sudo docker ps

Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemonrunning?

$ sudo service docker start

docker: unrecognized service
```

解决方案：

```bash
$ curl https://get.docker.com | sh

$ sudo service docker start
```
