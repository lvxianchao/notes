---
title: Homestead 配置 Xdebug 3 调试 Web 页面
abbrlink: f57e8b7a
date: 2021-12-25 16:21:13
categories:
 - 开发
tags:
 - homestead
 - xdebug
---

> 以下内容基于：
> 
> - Homestead v13.0.1
>   
> - PHP 8.0
>   
> - Xdebug v3.0.4
>   
> - Chrome 96.0.4664.110
>   
> - Chrome Extension: Xdebug Helper 1.6.1
>   

首先进入到 Homestead 里，切换到想要使用的 PHP 版本上，执行 `php -v` 查看信息。

如果没有 Xdebug 字样，则需要执行 `sudo phpenmod xdebug`来开启动 xdebug：

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251242520.png)

查看对应版本 PHP 的 xdebug 配置文件位置：

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251242639.png)

编辑这个配置文件，在文件末尾添加以下两行（如果你的 Xdebug 主版本号是 2 的话，这里的配置项会不同），保存退出：

```bash
xdebug.client_host=192.168.10.1

xdebug.mode=debug
```

执行 `sudo service php8.0-fpm restart`重启 FPM。

在 PHPStorm 里设置里，配置一个远程 CLI 解释器，选择 `Vagrant`:

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251242273.png)

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251242446.png)

`Vagrant 实例文件夹` 选择 Homestead 文件夹所在位置：

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251243072.png)

前往 Chrome 应用商店下载安装 [Xdebug helper - Chrome 网上应用店](https://chrome.google.com/webstore/detail/xdebug-helper/eadndfjplgieldjbigjakmdgkmoaaaoc)， 打开配置页面，配置 PHPStorm 的 IDE Key，然后开启 Debug 模式：

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251243821.png)

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251243126.png)

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251230024.png)

在代码里下个断点，触发断点后 PHPStorm 里会弹出如下窗口，选择`手动选择本文件或项目`后点击`接受`：

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202112251243177.png)

再次进入 PHPStorm 设置，选择 `PHP > 服务器` 可以看到刚刚配置的服务器，将**顶层目录**映射正确后即可正常 Debug：