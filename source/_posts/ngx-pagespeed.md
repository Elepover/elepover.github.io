---
title: 在 Arch Linux 上为 Nginx 启用 PageSpeed
date: 2018-01-18 02:03:14
tags:
---

## 关于 PageSpeed
PageSpeed 是一个用于优化 Web 服务器性能的模块，支持 Apache 和 Nginx。
具体见[此处](https://developers.google.com/speed/pagespeed/)<!-- more -->

本文只介绍如何在 Arch Linux 上基于[一个 AUR 项目](https://aur.archlinux.org/packages/nginx-mainline-mod-pagespeed/)，针对 Nginx 启用 PageSpeed 模块。
**1. 本教程不适合 Apache Server 或其他 Linux Distro.**
**2. 只支持 nginx-mainline 版本。**

## 开始
安装编译用必备软件包，一路回车就好。
`pacman -S base-devel`
如果你是默认 root, 新建一个专为编译软件包用的用户。
`useradd -m compile`
为其修改密码，键入指令后输入密码，回车后再输入一次密码确认。
`passwd compile`
修改 sudoers 以保证 sudo 得以正常执行。
`nano /etc/sudoers`
按下 `Ctrl + W`, 搜索 `root` 并回车。
在下一行键入:
`compile ALL=(ALL) ALL`
按 `Ctrl + X` 再按 Y, Enter 保存退出。
切换用户。
`su compile`
随后，让我们新建一个工作环境。
`mkdir /~/temp && cd /~/temp`
将最新的 Git Repo, Clone 下来。
`git clone https://aur.archlinux.org/nginx-mainline-mod-pagespeed.git`
如果你没有安装 git, 请先执行:
`yes | pacman -S git`
进入文件夹。
`cd nginx-mainline-mod-pagespeed`
开始编译。
`makepkg -sri`
随后会要求你输入先前设置的用户 compile 的密码，输入并回车，按 Y 安装。
退出 compile 用户，新建 PageSpeed 缓存文件夹。
`exit`
`mkdir /var/cache/ngx_pagespeed`
至此，PageSpeed 模块已经安装完毕了。我们需要启用它。

## 启用
现在编辑你的 Nginx 设置。（假设你是通过 pacman 安装的 Nginx.
`nano /etc/nginx/nginx.conf`
在文件顶部加入以下文本:
`load_module /usr/lib/nginx/modules/ngx_pagespeed.so;`

**此操作只会让 Nginx 载入 PageSpeed 模块而不是启用。请在需要启用的 `server` 块中填入以下内容:**
    add_header Access-Control-Allow-Origin *;
    pagespeed On;
    pagespeed FileCachePath "/var/cache/ngx_pagespeed/";
    pagespeed EnableFilters combine_css,combine_javascript;
Nginx 会自动在启动时载入编译好的 PageSpeed 模块。
保存退出，并重新启动 Nginx.
`systemctl restart nginx`
至此，PageSpeed 模块已经开始正常工作了，如需更加自定义的配置，请参阅 [PageSpeed Documentation](https://modpagespeed.com/doc/configuration).