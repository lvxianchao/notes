---
title: Windows 10 下的终端改善
abbrlink: ede84cf5
date: 2022-03-13 00:09:28
tags: 
  - Windows
  - Tabby
  - Git Bash
  - ZSH
  - Oh My Zsh
categories:
 - 折腾
---

个人习惯了 Mac 作为开发环境，但在某些环境下只能使用 Windows 10，感觉 Windows 10 对开发者非常不友好，尤其是终端，于是就稍微折腾改善一下，提升使用体验。

目前采用以下工具：

* Tabby
* Git Bash
* ZSH
* OhMyZsh

![](https://raw.githubusercontent.com/lvxianchao/images/main/imagesimage-20220311105634219.png)

很多人喜欢用 Windows Terminal 作为 Git Bash 的壳，我个人感觉它没有 Tabby 好用，Tabby 全方位碾压 Windows Terminal。

## Git for Windows

[Git for Windows](https://git-scm.com/download/win) 在安装的时候可以选择安装 Git Bash，我只安装了 Git Bash。

## ZSH

安装 ZSH 要稍微麻烦一些，前往 [这个地址](https://packages.msys2.org/package/zsh?repo=msys&variant=x86_64) 下载压缩包，可以看到这是一个 `zst` 格式的压缩包，如果你的解压软件不支持这个格式，还需要额外安装一个针对这种格式的解压工具 [PeaZip](https://peazip.github.io/peazip-64bit.html)。

因为受限于环境限制，无法在本机上安装 PeaZip，所以我是在 Ubuntu 虚拟机里安装了相关的解压缩工具，解压完成后弄到 Windows 10 物理机下。

```bash
# 安装 zstd
sudo apt install -y zstd

# 解压
zstd -d zsh-5.8-5-x86_64.pkg.tar.zst
```

![](https://raw.githubusercontent.com/lvxianchao/images/main/imagesimage-20220311111441169.png)

解压后的 tar 包里包含以下文件，先找个地方解压出来：

![](https://raw.githubusercontent.com/lvxianchao/images/main/imagesimage-20220311112654246.png)

解压出来的目录里，把 `zsh.exe` 删除掉，并把 `zsh-5.8.exe` 重命名为 `zsh.exe`，因为原来的 `zsh.exe` 大小为 `0`，Git Bash 里使用 ZSH 无法生效，调整后把整个文件夹复制到 Git 的安装目录下，比如我的 Git 安装在 `D:\Program Files\Git` 。

![](https://raw.githubusercontent.com/lvxianchao/images/main/imagesimage-20220311112936556.png)

## Oh My Zsh

执行以下命令安装：

```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

### zsh-autosuggestions

On My Zsh 自带了 Git 的插件，但我个人使用至少还需要一个插件：[zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)，这个插件可以**提示曾经使用过的命令并自动完成**，非常好用。

安装说明可以查看 [Github](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md) 。

## 将 ZSH 设置为默认 Shell

在 `.bashrc` 文件里，添加如下，然后执行 `source .bashrc`：

```bash
# Launch Zsh
if [ -t 1 ]; then
exec zsh
fi
```

自此，以后再启动 Git Bash 的时候就会默认切换到 ZSH 了。

## Tabby

[Tabby](https://tabby.sh/) 是一个现代化的终端程序，跨平台，支持大尺度定制化，UI 也非常养眼。

通过 Tabby 调用 Git Bash，以达到多标签和分屏等效果。

### 将 Git Bash 作为默认终端

在 `配置` 里，将 Git Bash 作为默认终端，再打开新的标签的时候就会默认使用 Git Bash。

![](https://raw.githubusercontent.com/lvxianchao/images/main/imagesimage-20220311142304843.png)

### 更改复制粘贴的快捷键

Windows 上有一个特别难受的地方就是在终端里复制粘贴的原生快捷键不再好用，但由于 Tabby 支持自定义快捷键，所以可以通过更改快捷键的方式来达到相同的目的。

在 `设置 > 快捷键` 中，分别为 `复制` 和 `粘贴` 增加上原生的快捷键。

### 其它

另外，我个人还在 `设置 > 终端` 里将 `右键点击` 调整成了 `右键菜单`，将剪贴板的 `选中时复制` 给关闭了，这些都看个人喜好吧。

## 最后

在 Windows 上开发，始终没有 Mac 上来的舒畅，**路漫漫其修远兮，吾将上下而求索**。
