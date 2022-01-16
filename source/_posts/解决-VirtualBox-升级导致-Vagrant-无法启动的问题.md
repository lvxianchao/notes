---
title: 解决 VirtualBox 升级导致 Vagrant 无法启动的问题
abbrlink: 77e0579c
date: 2022-01-16 11:51:45
tags:
  - VirtualBox
  - Vagrant
  - Homestead
categories:
  - 排错
---

今天把 VirtualBox 给升级到了 `6.1.30` 之后，发现 Homestead 无法启动了，报错如下：

```shell
➜  ~ vm up
Bringing machine 'homestead' up with 'virtualbox' provider...
==> homestead: Checking if box 'laravel/homestead' version '11.5.0' is up to date...
==> homestead: Clearing any previously set network interfaces...
The IP address configured for the host-only network is not within the
allowed ranges. Please update the address used to be within the allowed
ranges and run the command again.

  Address: 192.168.10.10
  Ranges: 192.168.56.0/21

Valid ranges can be modified in the /etc/vbox/networks.conf file. For
more information including valid format see:

  https://www.virtualbox.org/manual/ch06.html#network_hostonly
```

去到 `https://www.virtualbox.org/manual/ch06.html#network_hostonly` 这个地址已经给出了相关的解决办法：

```shell
sudo mkdir /etc/vbox

sudo vim /etc/vbox/networks.conf
```

并将以下内容写入到 `/etc/vbox/networks.conf` 即可解决：

```
* 10.0.0.0/8 192.168.0.0/16
* 2001::/64
```

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201161129832.png)