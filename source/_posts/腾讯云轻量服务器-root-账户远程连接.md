---
title: 腾讯云轻量服务器 root 账户远程连接
tags:
  - CentOS
abbrlink: 6765f8ce
date: 2022-03-16 23:22:07
---

# 腾讯云轻量服务器使用 root 远程连接

今天在使用腾讯云轻量服务器的时候，安装的是 CentOS 7.6 的操作系统，发现安装完以后使用自带的网页版远程连接工作登录上去是一个叫 `lighthouse` 的用户，并且在我本地机器上无法使用 `root` 账户远程连接，大概意思没有权限。

## 更改 root 用户密码

在提供的网页版远程连接上去以后执行 `sudo -i` 切换到 `root` 账户，并执行 `passwd` 修改密码。

## 开启 root 用户的远程连接

编辑 `sshd` 服务的配置文件：

```bash
vim /etc/ssh/sshd_config
```

打开 `PermitRootLogin yes`,将 `PasswordAuthentication` 的值修改为 `yes`，保存退出。

重启 `sshd` 服务：

```bash
systemctl restart sshd
```
