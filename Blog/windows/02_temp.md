# hyper-v 共享文件夹

## 前言

hyper-v 上安装了 Ubunt 桌面版，打算做开发环境。然而发现虚拟化的系统不是很流畅，这样违背了我的初衷。

换个思路，在 hyper-v 上安装 Ubunt-server 这样就解决了开发不流畅的问题，接踵而至的是他并不提供界面话处理，所以在宿主机安装 IDE 将文件夹共享给 Ubunt-server 让 Ubunt-server 来启动环境，宿主机只负责修改文件。

## 内容

### 环境依赖

1. windows 10 旗舰版 (宿主机)
2. hyper-v (虚拟化软件)
3. Ubnut-server.ios (系统镜像)
4. D:\SharedFolder (创建共享文件夹)

## 总结

### 相关资料

- [博客参考](https://linuxhint.com/shared_folders_hypver-v_ubuntu_guest/)

