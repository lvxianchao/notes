---
title: MacOS 12 Monterey 黑苹果在 Windows 下的 USB 定制
tags:
  - MacOS
  - 黑苹果
  - USBToolBox
  - ProperTree
  - DiskGenius
categories:
  - 黑苹果
abbrlink: cc238e6d
date: 2022-03-13 00:23:11
---

# MacOS 12 Monterey 黑苹果在 Windows 下的 USB 定制

> 2022 年春节以后，我把以前的黑苹果从 11 升级到了 12，睡眠问题一直存在，经常会 **半夜的时候自动唤醒，然后风扇疯狂地转动，但显示器就是不亮，鼠标和键盘也无法唤醒，只能强制关机或者直接断电重新开机**，相当烦，但一直懒得没动。
>
> 最近，因为疫情的原因导致需要在家里办公，需要先连接 VPN，然后使用 Microsoft Remote Desktop 远程连接到公司的 Windows 10 电脑上去，中间过程还挺麻烦的，我不想因为睡眠问题总是需要断电重启，于是今天折腾一下，把 USB 定制给解决掉，看看能不能修复我的黑苹果睡眠问题。
>
> 因为在 MacOS 12 上 `XhciPortLimit` 无法使用，所以才选择在 Windows 10 上来做 USB 定制。

## 用到的工具

* [USBToolBox](https://github.com/USBToolBox)
  * [tool](https://github.com/USBToolBox/tool) 用来做 USB 定制并生成 `kext` 文件。
  * [kext](https://github.com/USBToolBox/kext) 定制出来的 `kext` 文件需要依赖于这个。
* [ProperTree]([corpnewt/ProperTree: Cross platform GUI plist editor written in python. (github.com)](https://github.com/corpnewt/ProperTree)) 用来编辑 `config.plist` 文件。
* [DiskGenius](https://www.diskgenius.cn/) 用来操作黑苹果的 EFI 里的文件。
* [Python 3](https://www.python.org/) ProperTree 是 Python 写的，需要安装。
* 一个 USB 2.0 设备，如 U 盘、鼠标、键盘等。
* 一个 USB 3.0 以上的设备，如 U 盘、移动硬盘等。
* 一个 Type C 的设备，如手机等（我的主板是华硕的 `z390-i`，后面板上有一个 USB C，如果你的主板没有，可以忽略）。

## 生成 USB 定制文件

### 下载 USBToolBox - tool

直接在 [Releases · USBToolBox/tool ](https://github.com/USBToolBox/tool/releases) 里下载最新版本的 `Windows.exe`，我当前的最新的版本是 `0.1.1`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130018973.png)

### 识别接口

打开 `Windows.exe`，如下图所示，输入 `D` 并回车，大小写没关系。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130018126.png)

接下来的界面会每 5 秒刷新一次，**绿色**代表当前已经插入的设备，**蓝色**代表刚刚已经识别到的并且已经拔出的设备。

首先将 2.0 设备插入每一个接口，并等待界面刷新，看到有新的绿色的条目出现，再拔出。

3.0 的设备重复一次。

Type C 的设备需要正反两面都插拔一次。

操作好以后，输入 `B` 并回车。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130019823.png)

### 生成 UTBMap.kext 文件

回到了菜单界面，输入 `S` 并回车会看到整体 USB 识别情况。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130029493.png)

输入 `K` 并回车，会看到生成了 `UTBMap.kext` 文件和其所在的路径，默认会放到 `下载` 目录下。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130019304.png)

### 编辑 UTBMap.kext

因为苹果对 USB 接口的数量限制到了 15 个，所以接下来需要编辑 `UTBMap.kext` 里接口，多于 15 的话需要删除。

我这里因为正好满足 15 个接口，无法演示，可以另行搜索相关资料。

如果你和我一样没有大于 15 个则可以跳过这一步。

## 下载 USBToolBox - kext

在 [Github](https://github.com/USBToolBox/kext/releases) 里下载最新的 `release` 版本。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130019021.png)

将压缩包里的 `USBToolBox.kext` 解压出来和刚刚生成的 `UTBMap.kext` 放在一起，以便稍后使用。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130020427.png)

## 更新 EFI

### 备份

下载安装 [DiskGenius](https://www.diskgenius.cn/) 并打开，将左侧 EFI 分区里的 EFI 文件夹，右键 `复制到桌面` 进行备份，也可以选择其它位置。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130020288.png)

### 添加 kext 文件

将刚刚准备好的 `USBToolBox.kext` 和 `UTBMap.kext` 通过 DiskGenius 复制到 `EFI/OC/Kexts ` 目录下。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130021950.png)

### 编辑 config.plist

将 OC 文件夹下的 `config.plist` 文件，右键 `复制到桌面`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130021393.png)

使用 ProperTree 打开已经复制到桌面的 `config.plist` ，先全部折叠起来。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130021650.png)

依次打开 `Root -> Kernel -> Add`，找到最后一个条目，复制粘贴两次，并按照下图中我第 `9` 、`10` 号元素那样编辑好，保存。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130021398.png)

在同级条目里找到 `USBInjectAll.kext` 并将 `Enabled` 设置为 `False`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130022032.png)

在 `Add` 的同级条目 `Quirks` 下，找到 `XhciPortLimit` 将其设置为 `False`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202203130022569.png)

将保存好的 `config.plist` 文件替换回去，重启到 MacOS 观察各个 USB 接口的识别情况和速率。

