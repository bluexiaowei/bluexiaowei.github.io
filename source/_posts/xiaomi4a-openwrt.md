---
title: 小米路由器 4A 安装 OpenWrt 软路由系统
date: 2022-08-07
tags: 小米路由器 OpenWrt 软路由
---

## 前置依赖

1. 小米路由器 4A(百兆版)
2. OpenWrt
3. [OpenWRTInvasion](https://github.com/acecilia/OpenWRTInvasion)
4. [MIWIFIRepairTool](http://miwifi.com/miwifi_download.html)（非必要）
5. Macos / Windows / Linux 电脑

## 刷机步骤

### 执行脚本

```bash
git clone https://github.com/acecilia/OpenWRTInvasion.git

cd OpenWRTInvasion

# 安装依赖
pip3 install -r requirements.txt
# 执行脚本
python3 remote_command_execution_vulnerability.py
# 按照提示操作...
```

### 安装 OpenWrt

```bash
## 输入用户名 root 密码 root
telnet miwifi.com

# 进入临时目录，路由器重启目录下的文件会被删除
cd /tmp

# 下载 OpenWrt 镜像
wget http://mirrors.ustc.edu.cn/openwrt/releases/19.07.9/targets/ramips/mt76x8/openwrt-19.07.9-ramips-mt76x8-xiaomi_mir4a-100m-squashfs-sysupgrade.bin -O firmware.bin

## 安装系统
mtd -e OS1 -r write firmware.bin OS1

## 等待刷机中...，路由器闪黄灯代表重启，不闪了代表重启成功
```

### 设置二级路由

`ssh root@192.168.1.1`

编辑文件网络配置文件 `vim /etc/config/network ` 中的 `config interface 'lan'` 配置

`option ipaddr '192.168.1.1'` => `option ipaddr '192.168.31.1'`

保存文件，并重启路由器 `reboot`

进入 web 操作页面 http://192.168.31.1

## 设置 Wifi

位置：http://192.168.31.1 > 网络 > 无线

2.4G: MediaTek MT76x8 802.11bgn
5.0G: MediaTek MT76x2E 802.11acn

SSID 是 Wifi 名称，加密选 WPA2-PSK

小米默认命名

- 2.4G: xxx
- 5G: xxx_5G

## 安装插件

- luci-i18n-base-zh-cn
- [smartdns](https://github.com/pymumu/smartdns)
- luci-app-smartdns

### luci-i18n-base-zh-cn

web 界面 > System > Software > Update lists... > Filter(luci-i18n-base-zh-cn) > install

### smartdns

获取资源

```bash
wget https://github.com/pymumu/smartdns/releases/download/Release37/smartdns.1.2022.08.06-1602.mipsel-openwrt-all.ipk -O smartdns.ipk
wget https://github.com/pymumu/smartdns/releases/download/Release37/luci-app-smartdns.1.2022.08.06-1602.all-luci-all.ipk -O luci-app-smartdns.ipk

# 发送到路由器
scp smartdns.ipk luci-app-smartdns.ipk root@192.168.31.1:/tmp
```

安装插件 `ssh root@192.168.31.1`

```
cd /tmp

opkg install smartdns.ipk && opkg install luci-app-smartdns.ipk

reboot
```

设置 smartdns 参数，进入 http://192.168.31.1 可视化配置界面。服务 > SmartDNS

![](image.png)

配置 DNS 服务商

| DNS 服务器名称 | DNS 服务器 IP   | 协议类型 |
| -------------- | --------------- | -------- |
| 腾讯云         | 119.29.29.29    | udp      |
| 阿里云         | 223.5.5.5       | udp      |
| 144            | 114.114.114.114 | udp      |
| baidu          | 180.76.76.76    | udp      |
| Google         | 8.8.8.8         | udp      |

点击 “保存并应用”

## 切换 OpenWrt 源

位置：系统 > SoftWare（软件）> Configure opkg > opkg/distfeeds.conf

替换地址：downloads.openwrt.org => mirrors.tuna.tsinghua.edu.cn/openwrt

使用浏览器搜索功能 ctrl + F 标注 downloads.openwrt.org 这样页面中的关键字就会高亮，再一个一个替换。确保地址的正确性。

最后更新源：页面按钮 `Update lists` 或者路由器命令 `opkg update`

## 路由器变砖教程

- [小米路由器修复工具 MIWIFIRepairTool](https://bigota.miwifi.com/xiaoqiang/tools/MIWIFIRepairTool.x86.zip)
- [4A（百兆版） ROM](https://bigota.miwifi.com/xiaoqiang/rom/r4ac/miwifi_r4ac_firmware_e9eec_2.18.58.bin)
- [官方操作教程](https://web.vip.miui.com/page/info/mio/mio/detail?postId=19134127&app_version=dev.20051)
- **需要 Windows 操作系统**

### MIWIFIRepairTool

解压 MIWIFIRepairTool.zip 压缩

关闭系统防火墙 `控制面板\系统和安全\Windows Defender 防火墙\自定义设置` 关闭专有网络和公共网络防火墙

打开 MIWIFIRepairTool.exe 程序，本地上传 `miwifi_r4ac_firmware_e9eec_2.18.58.bin`4A ROM 文件

选择网卡：以太网（网线连接到小米路由器的网卡，并且是同一个网段的 192.168.31.xxx）

断开路由器电源，按住 Reset，插下电源知道**黄灯闪烁**。此时电脑端会弹出刷机进度条。等待路由器刷到**蓝灯闪烁**，插拔路由器电源就行了。

### FQA

1. OpenWRTInvasion 刷 OpenWrt 导致路由器连不上 ping 不通

   > 不要使用 OpenWRTInvasion README.md 中的 OpenWrt 镜像，请使用文章中的镜像

2. MIWIFIRepairTool 刷机没反应

   > 看看防火墙是否已关闭，网卡选择是否正确，网线是否连接，且连接 Lan 口（不是互联网接口）

3. v-2-r-a-y 使用失败

   > 我也没有解决，请高手手把手教程

4. nslookup -querytype=ptr smartdns 测试失败

   > 请看官方 README.md 教程。排查 **重定向：重定向 53 端口到 SmartDNS**

5. 一直提示服务器为设置密码

   > http://192.168.31.1 > 系统 > 管理权，设置路由器密码。

6. 有些配置不生效

   > 再次检查配置，是不是忘记点击 “保存并应用” 按钮

7. 有些插件不知道下载什么编译版本

   > 在路由器中使用 `opkg print-architecture` 查看 => `arch mipsel_24kc 10`
