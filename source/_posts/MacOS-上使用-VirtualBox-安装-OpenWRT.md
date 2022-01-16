---
title: MacOS 上使用 VirtualBox 安装 OpenWRT
categories:
  - 折腾
tags:
  - Mac
  - VirtualBox
  - OpenWRT
  - 虚拟机
abbrlink: 7d336898
date: 2022-01-16 11:36:30
---

> 临近年关了，由于一定的原因春节期间需要背着笔记本到处走，就提前收拾了一下笔记本：
>
> 1. 把磁盘给格了，干干净净的。
> 2. 重新安装了系统，升级到了最新版，舒服。
> 3. 布置平时开发需要用到的环境和软件等。
> 4. OpenWRT 必须安排上，懂得都懂。

平时在家里都是直接在我的 J4125 上全局 OpenWRT，但笔记本的网络环境还是安排到本地虚拟机里比较方便。

我是用的 eSir 编译的固件，下载好，把 `.gz` 解压成 `.img`，如果你是下载的 OpenWRT 官网的镜像则不需要了。

## 准备镜像

OpenWRT 的 `.img` 文件需要先转换成 VirtualBox 所能识别的 `.vdi` 虚拟硬盘文件。

```bash
VBoxManage convertfromraw --format VDI openwrt-spp-winter\[2022\]-x86-64-generic-squashfs-uefi.img openwrt.vdi
```

看到如下信息代表没啥问题：

```bash
Converting from raw image file="openwrt-spp-winter[2022]-x86-64-generic-squashfs-uefi.img" to file="openwrt.vdi"...
Creating dynamic image with size 541359616 bytes (517MB)...
```

## VirtualBox 设置

### 创建虚拟机

打开 VirtualBox ，然后点击 `新建` 来新建一个虚拟机。

* 名称可以随意填写；
* 类型选择 `Linux`；
* 版本选择 `Linux 2.6/3.x/4.x(64-bit)`。

点击 `继续`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161049374.png)

内存默认给到 1024 M 就足够了，点击 `继续`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050509.png)

接下来，先把刚刚转换好的 `openwrt.vdi` 文件移动到 `/Users/lvxc/VirtualBox VMs/OpenWRT`，这个路径就是正在创建的虚拟机的位置。

在 `虚拟硬盘` 窗口里，选择 `使用已有的虚拟硬盘文件`，点击右侧的 `文件` 图标弹出虚拟介质管理器，点击 `注册` ，选择 `openwrt.vdi`，点击 `选择`，点击 `创建`，即可完成虚拟机的创建。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050019.png)

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050594.png)

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050556.png)

### 虚拟机设置

#### VirtualBox 设置

在 VirtualBox 的菜单栏里，点击 `管理`，在弹出来的 `主机网络管理器` 窗口中点击 `创建` ，点击 `关闭`，这时候我们就创建了一个 `仅主机(Host-only) 网络适配器`。

![](/Users/lvxc/Library/Application Support/typora-user-images/image-20220115221154848.png

!(/Users/lvxc/Library/Application Support/typora-user-images/image-20220115221128330.png)

#### 虚拟机网络设置

在虚拟机 `OpenWRT` 的 `设置` 中，选择 `网络` ，将 **网卡 1** 的连接方式设置为 `桥接网卡`。

* `界面名称` 设置为当前笔记本连接网络的方式，也就是 `en0: Wi-Fi(AirPort)`。
* `高级` - `控制芯片` 选择 `Intel PRO/1000 MT 服务器（82545EM）`。
* `高级` - `混杂模式` 选择 `全部允许`。

点击 `OK` 保存，并启动虚拟机。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050124.png)

### 修改网络

输入以下命令，将 IP 修改成同一网段里的不重复的 IP，比如我这里用  `192.168.1.100` ，重新启动虚拟机：

```bash
uci set network.lan.ipaddr='192.168.1.100'

uci commit

reboot
```

虚拟机重启以后，便可以在浏览器里输入 `192.168.1.100` 来访问 OpenWRT 的 web 控制界面了，如果没有修改过密码，默认为没有密码。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050359.png)

在 `网络` - `接口` - `LAN` 中，设置 `IPv4 网关` 为当前网络的网关 `192.168.1.1`，然后在下面的 `基本设置` 里勾选 `忽略此接口`，保存并应用。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161050163.png)

在 `网络` - `Turbo ACC 网络加速` 中勾选 `DNS 缓存` 并设置上游 DNS 服务器，保存并应用。

看一下 `网络` - `DHCP/DNS` 里的 `DNS 转发` 是不是 `127.0.0.1#5333`，是这个就没问题了。

### 物理机使用

至此，虚拟机里的 OpenWRT 就算是基本可以用了，在 Mac 主机上，可以通过手动设置 IP 地址，将 `路由` 设置为 OpenWRT 的 IP 地址，然后添加一个 DNS 为 OpenWRT 的地址，便可以走 OpenWRT 了。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161051054.png)

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161051437.png)

OK，关机备用。

