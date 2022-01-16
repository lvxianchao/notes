---
title: CentOS 7 部署 Gogs 私有代码仓库
categories:
  - 运维
tags:
  - CentOS
  - Gogs
  - 代码仓库
abbrlink: 7ffb7ac
date: 2022-01-10 22:24:51
---

## 设置 Git 用户

1. 创建 Git 用户

```bash
adduser git
```

2. 给 `/etc/sudoers` 添加写权限

```bash
chmod +w /etc/sudoers
```

3. 把 Git 用户加进 `sudoers`，并设置 sudo 的时候不用输入密码

```bash
git     ALL=(ALL)       NOPASSWD:ALL
```

## 搭建 Gogs

1. 进入到 Git 用户的家目录并下载 Gogs 的进制包，最新版本的二进制包可以去[官网](https://gogs.io/docs/installation/install_from_binary)查看。

```bash
cd /home/git && wget https://dl.gogs.io/0.12.3/gogs_0.12.3_linux_amd64.tar.gz
```

2. 解压

```bash
tar -zxvf gogs_0.12.3_linux_amd64.tar.gz
```

3. 进入到刚刚解压好的 gogs 目录，使用 Git 用户临时启动 Gogs 服务

```bash
cd gogs && sudo -u git ./gogs web
```

4. 创建一个 Gogs 数据库

```sql
create database gogs default CHARSET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

5. 创建一个只有本地可以连接的 `gogs` 用户，密码是 `password`，**注意将密码修改你自己的**。

```sql
create user 'gogs'@'127.0.0.1' identified by 'password';
```

6. 给 `gogs` 用户分配 `gogs` 数据库的所有权限

```sql
GRANT ALL PRIVILEGES on gogs.* to 'gogs'@'127.0.0.1';
```

这时候就可以通过服务器的 `IP:3000` 访问了 Gogs 的 Web 页面了，我用 Nginx 配了反向代理，所以直接访问 `IP` 即可。

在 Web 页面上配置好**数据库信息**、**仓库根目录**、**运行系统用户**等信息，点击`立即安装`即可。

关于`管理员账号设置`，可以这个页面中直接设置，也可以安装完成以后再去注册，Gogs 的规则是：**第一个注册的用户**就是管理员。

如下是我的配置：

![](https://coderlxc-1256179520.file.myqcloud.com/20210812/dI4Ob8.png)

## 把 Gogs 作为服务运行并配置开机自启动

1. 把 Gogs 官方提供的 `gogs.service` 模板复制到 `/etc/systemd/system` 目录下：

```bash
cp /home/git/gogs/scripts/systemd/gogs.service /etc/systemd/system
```

2. 修改 `gogs.service` 模板，如果你是按照上面的步骤操作的则没有什么需要修改的。
3. 重新加载 `systemd`:

```bash
systemctl daemon-reload
```

4. 启动 Gogs 服务

```bash
systemctl start gogs
```

5. 加入开机自启动

```bash
systemctl enable gogs
```
