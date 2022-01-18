---
title: Ubuntu 经验积累
tags:
  - ubuntu
abbrlink: 7be7930f
date: 2022-01-18 22:22:45
---

## Ubuntu 20.04 更改系统时区

可以使用 `date -R` 查看当前系统时间。

运行 `tzselect`。

1. 选择亚洲

2. 选择国家和城市

3. 确认

4. 最后执行 `sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime` 覆盖配置。
5. 再次执行 `date -R` 检查结果。