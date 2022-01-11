---
title: CentOS 8 部署 Yapi
tags:
  - centos
  - yapi
  - 部署
abbrlink: 2a8401d8
date: 2022-01-11 19:26:48
---

## Node

1. 查看可用的模块，`d` 代表默认的版本：
   
   ```shell
   yum module list nodejs
   ```

    ![image-20210818145614914](https://coderlxc-1256179520.file.myqcloud.com/20210818/BfkYTI.png)

2. 安装默认版本
   
   ```shell
   yum module install nodejs
   ```

3. 配置 NPM 淘宝镜像

    ```shell
    npm config set registry https://registry.npm.taobao.org
    ```

## MongoDB

1. 创建 yum 配置文件

    ```shell
    vim /etc/yum.repos.d/mongodb-org-4.2.repo
    ```

2. 编辑配置文件填入以下内容：

    ```shell
    [mongodb-org-4.2]
    name=MongoDB Repository
    baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/x86_64/
    gpgcheck=1
    enabled=1
    gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
    ```

3. 安装 MongoDB

    ```shell
    yum install -y mongodb-org
    ```

4. 启动 MongoDB 服务

    ```shell
    systemctl start mongod
    ```

5. 配置开机启动

    ```shell
    systemctl enable mongod
    ```

6. 查看是否配置开机自启成功

    ```shell
    systemctl list-unit-files|grep mongod
    ```

## Yapi

1. 安装 Yapi

    ```shell
    npm install -g yapi-cli
    ```

2. 启动可视化安装服务

    ```shell
    yapi server
    ```

3. 填写相关配置信息后，点击`开始部署`，等待完成后记住`管理员邮箱账号`和`密码`

![image-20210818150949211](https://coderlxc-1256179520.file.myqcloud.com/20210818/lWVmYf.png)

4. 切换到 Yapi 的部署位置启动服务，此时可通过 web 来访问 Yapi 服务了

    ```shell
    cd /root/my-yapi && node vendors/server/app.js
    ```

## PM2 守护进程

1. 安装 PM2

    ```shell
    npm install -y pm2
    ```

2. 使用 PM2 启动 Yapi，并设置占用内存超过 500M 后重新启动服务：

    ```shell
    pm2 start /root/my-yapi/vendors/server/app.js -n yapi --max-memory-restart 500M
    ```
