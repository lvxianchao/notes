---
title: Jetbrains Code With Me 私有部署
tags:
  - Jetbrains
  - Code With Me
abbrlink: 8e1b3a1e
date: 2022-04-10 11:50:56
---

# Jetbrains Code With Me 私有部署

> 最近因为疫情的原因在家办公，通过 VPN + Remote Desktop 的方式在公司的电脑上写代码，延迟非常大，严重影响效率。
>
> 资源地址：[https://www.jetbrains.com/zh-cn/code-with-me/on-prem/](https://www.jetbrains.com/zh-cn/code-with-me/on-prem/)

我想起了 `Code With Me` 这个东西，于是试用了一下感觉还不错，但因为默认的服务器是 Jetbrains 的，网络原因会导致有时候也会卡，于是就想着试试用我自己在国内的服务器搭建一个私有的服务器试试效果如何，结果体验下来非常棒，网络延迟基本感觉不到，记录一下。

在 `Code With Me` 的设置里可以看到，默认使用的服务器是 Jetbrains 的，我们只需要把这个 `大厅服务器 URL `替换成自己的就行了。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202204101159820.png)

在 `资源地址  `里，点击 `注册免费试用`，填写表单提交以后，会收到 Jetbrains 发来的邮件。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202204101208752.png)

邮件里包含服务器的搭建文档和两个附件，附件里有一个 `.key` 结尾的许可证文件，下载下来，搭建服务器的时候需要用到。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202204101206909.png)

远程登录到服务器上，把 `资源地址` 里的 `快速设置启动器` 里的 `Linux x86-64.tar.gz` 文件包下载下来并解压，我这里的解压以后的文件夹名称是 `code-with-me-quick-setup-1744`，进入到这个文件夹里，把从邮件里下载到 `key` 文件上传到这个文件夹中。

运行以下命令便可以启动服务器，需要把 `服务器 IP` 和 `Code_With_Me_Enterprise-XXXXXXX.key` 换成你自己的：

```bash
./launch.sh -h 服务器 IP -k Code_With_Me_Enterprise-XXXXXXX.key
```

服务运行起来以后，把 IDE 的 `大厅服务器 URL` 替换成：`http://服务器IP:2093` 就可以了。
