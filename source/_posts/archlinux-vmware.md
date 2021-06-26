---
title: Arch 折腾记 - 在 VMWare 上简易地安装 Arch Linux
date: 2018-02-22 15:27:06
tags:
- arch linux
- arch
- vmware
thumbnail: https://i.yecdn.com/images/2018/02/22/b35b4d3ddad4524e4466c99375942b44.png
---

手动安装 Arch 是一件非常痛苦的事情（不

虽然 VMWare 提供了「简易安装」这个 Feature，然而 Arch 压根没提供任何 OOBE 向导，全程命令行操作，也相对提高了门槛。

这篇文章将带领你以**相对 Dirty** 的方式，在 VMWare 上较轻松地完成 Arch 的安装程序。<!-- more -->

> 如果你已经有过在虚拟服务器（VPS）提供商安装 Arch 的经验，教程的内容可能相当熟悉。

# 准备

安装环境：

- VMWare Workstation 12 (On Windows 10)
- CentOS 7 (Minimal) x86_64 ISO
- 正常的网络连接

# 开始安装

VMWare 12 是不支持 Arch 的简易安装的 ~~（想支持也没法支持）~~

可以看到，当你选择 Arch 镜像的时候: 

![Not Supported](https://i.yecdn.com/images/2018/02/22/4263174c8225090117110605a0e56bfd.png)

↑ 就很尴尬...

但是我们需要选择 CentOS 7 Minimal 镜像: 

![CentOS](https://i.yecdn.com/images/2018/02/22/9840b11c78175edb13f9b28d2b2d4a4f.png)

↑ OK，我们可以继续配置了。

完成后，启动配置好的虚拟机，按照正常安装步骤进行。

![Installation](https://i.yecdn.com/images/2018/02/22/4d169ee251e97c7d58e788c93bcc4671.png)

↑ （此处按回车启动安装程序）

![Installation_2](https://i.yecdn.com/images/2018/02/22/c023733c688b909a5185a0b8819fe9d3.png)

↑ 此处可选中文（如果看不懂的话）

因为安装 Arch 时这些配置基本会重置。

默认网络配置会无法连接网络，因为系统无法检测。

![Pre-Configuration](https://i.yecdn.com/images/2018/02/22/ee6c0e47eeee7f15971564b8b400d449.png)

↑ 先点击 `NETWORK & HOST NAME` 栏。

![Turning_On](https://i.yecdn.com/images/2018/02/22/853116627a1a09976b92b5171c451e72.png)

↑ 启用网络连接（右上角）

再点击左上角 `Done` 回到原界面。

再戳 `SECURITY POLICY`（安全策略）

将 `Apply security policy` 选项关闭。

点击 `Done` 返回。

点击 `Begin Installation` 按钮，开始安装。

![Setting_Password](https://i.yecdn.com/images/2018/02/22/8092219b300b665cb4cbae835bb68fc0.png)

↑ 在这个画面，设置好 `root` 用户密码。

请注意符合密码强度需求。

安心等待安装完成，点击重新启动。

# 安装 Arch Linux

在前文中已经提到: 

> 如果你已经有过在虚拟服务器（VPS）提供商安装 Arch 的经验，教程的内容可能相当熟悉。

没错，我们要用到 `vps2arch` 工具。

使用 `root` 用户名和之前设置好的密码，登录到安装好的 CentOS。

执行以下命令（可复制粘贴）: 

```
yum -y install wget
wget https://git.io/vps2arch
chmod +x vps2arch
./vps2arch
```

等待系统安装完毕。

![Downloading](https://i.yecdn.com/images/2018/02/22/45d6896d781b03a5064be9cd56d06a99.png)

↑ 正在准备 Arch 环境（下载依赖）。

（不应该叫「依赖」...不过我也不知道叫什么好了23333（逃

![pacman](https://i.yecdn.com/images/2018/02/22/96d923710f4fa93d4ae0c618c34a94ce.png)

↑ 熟悉的 `pacman` 出现啦！

![error](https://i.yecdn.com/images/2018/02/22/cbf54306285567c530c4b9098f6e5bd7.png)

↑ 中途出错可以无视，只要没 kill 掉 pacman.

待转换脚本执行完毕，执行以下命令: 

`sync ; reboot -f`

重启后，使用 `root` 用户和原密码登录新系统。

如有需要，可以执行 `pacman -Syyu base-devel` 以安装编译依赖包。

![Finish](https://i.yecdn.com/images/2018/02/22/b35b4d3ddad4524e4466c99375942b44.png)

~~↑ \ 享受滚包的快感吧 /~~
