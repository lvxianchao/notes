---
title: Gogs 部署 WebHook
categories:
  - 运维
tags:
  - Gogs
  - WebHook
  - 代码仓库
abbrlink: b141a6da
date: 2022-01-18 22:14:30
---

# Gogs 部署 Webhook

Gogs 同时支持 Git 和 Web 方式的钩子，Git 方式比较适合 Gogs 代码仓库和项目部署在同一台服务器上的情况，Web 钩子适合代码仓库和项目部署在不同服务器上的情况。无论是哪种情况，Web 钩子都可以做到，相比于 Git 钩子，Web 钩子要稍微麻烦一点，因为是跨服务器的，需要写脚本，调用接口。



假设我的 Gogs 服务器部署在 A 服务器上，然后项目部署在 B 服务器上，那么配置好 Gogs 的 Web 钩子以后，仓库在收到了你订阅的操作以后，会调用你配置的接口地址（这个接口应该是 B 服务器提供），把相关信息以 API 的方式推送给 B，然后 B 服务器在收到 API 推送请求以后，应该根据情况，如仓库、分支、事件等进行相关逻辑操作。



Gogs 的 Web 钩子配置的地方在 `仓库设置 -> 管理 Web 钩子`，在`添加新的 Web 钩子`处选择 `Gogs`：

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201182213448.png)

在之后跳出来的页面里配置上`推送地址`、和 `密钥文本` 后点击 `添加 Web 钩子`，会提示添加钩子成功，再次点击钩子名称可以编辑钩子。

在编辑钩子的页面的底部，可以查看到`推送记录`和进行`测试推送`。

这里我以本机为例，在 Nginx 中配置一个 `vhost` 监听 `8080 `端口并把请求转发给 PHP 来处理，下面是 PHP 脚本里的内容：

```php
<?php

$request_body = file_get_contents("php://input");

if (empty($request_body)) {
    die('received request body is empty.');
}

$content = json_decode($request_body, true);

switch ($content['repository']['name']) {
    // 匹配项目名称
    case 'gogs-webhook-test':
        switch ($content['ref']) {
            // 匹配分支
            case 'refs/heads/master':
            		// 要执行的操作：进入到项目所在目录，拉取最新代码。
                exec("cd /home/wwwroot/gogs-webhook-test/ && git pull origin master");
                break;
            default:
                break;
        }
        break;
    default:
        break;
}
```

在测试推送之前，可以先准备一下部署密钥，方便部署。先给 `www` 用户安排上密钥，输入以下命令一路回车即可：

```bash
sudo -Hu www ssh-keygen -t rsa
```

然后查看一下公钥内容，并复制下来：

```bash
cat /home/www/.ssh/id_rsa.pub
```

在 Gogs 的 `仓库设置`中，点击`管理部署密钥`，点击`添加部署密钥`，标题起个有意义的，有分区度的比如：`XX 服务器 www 用户` 这种，然后把刚刚复制下来的公钥内容填入到`密钥文本`中点击下方的`添加部署密钥`。

![](https://raw.githubusercontent.com/lvxianchao/images/main/images/202201182213315.png)

接下来，以 `www` 用户的身份把项目克隆到服务器上，然后随便做点什么更改便可观察测试是否有自动拉取最新的代码。如果禁用 `exec()` 函数等，一定要解禁。

