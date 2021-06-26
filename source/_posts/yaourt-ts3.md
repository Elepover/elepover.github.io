---
title: Arch 折腾记 - 安装 yaourt 以及搭建 TeamSpeak 3 服务器
date: 2018-01-18 02:04:57
tags:
---

这篇文章其实是~~一篇水文~~（

## 关于 AUR 和 yaourt

[ArchWiki][1] 上已有详细介绍，在此不再赘述。

官方仓库内的大量二进制软件包以及 AUR 中的其他软件包，使得 Arch 的既有软件包数量多到上天，也大幅降低了编译安装的繁琐程度。

## 安装 yaourt

以下是一个非常直截了当的 [yaourt][2] 安装教程。<!-- more -->

**更新系统: **
`pacman -Syyu`

**安装编译依赖包: **
`pacman -S base-devel`

**创建用于 yaourt 运行的新用户: **
`useradd -m aur`

**修改密码: **
`passwd aur`

**修改 sudoers: **
`nano /etc/sudoers`
寻找 `root ALL=(ALL) ALL` 并在其下方加入一行: 
`aur ALL=(ALL) ALL`

**切换用户: **
`su aur`
`cd ~`

**获得 `package-query` 依赖包: **
`git clone --depth=1 https://aur.archlinux.org/package-query.git package-query && cd package-query`

**编译安装: **
`makepkg -sri`

会提示你输入 `aur` 用户密码，输入之前设置的即可。

**安装 yaourt: **
`git clone --depth=1 https://aur.archlinux.org/yaourt.git yaourt && cd yaourt && makepkg -sri`

然后就搞定咯。

## 安装 TeamSpeak 3 Server

直接使用 yaourt 安装。

切换到 `aur` 用户，运行安装: 
`su aur`
`yaourt -S teamspeak3-server`

切换回 `root`
`su root` （如果你是由 `root` 切换进 `aur` 的，你也可以直接 `exit`）

启动 TS3 服务，并设置开机启动

`systemctl start teamspeak3-server && systemctl enable teamspeak3-server`

查看 TS3 服务状态，从中获得 `Access Key`.

示例（来自 ArchWiki）

```
● teamspeak3-server.service - TeamSpeak3 Server
   Loaded: loaded (/usr/lib/systemd/system/teamspeak3-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2015-09-05 23:34:42 BST; 49min ago
 Main PID: 20126 (teamspeak3-serv)
   CGroup: /system.slice/teamspeak3-server.service
           └─20126 /usr/bin/teamspeak3-server logpath=/var/log/teamspeak3-server/ dbsqlpath=/usr/share/teamspeak3-server/sql/

Sep 05 23:34:45 Your-Hostname teamspeak3-server[20126]: serveradmin rights for your virtualserver. please
Sep 05 23:34:45 Your-Hostname teamspeak3-server[20126]: also check the doc/privilegekey_guide.txt for details.
Sep 05 23:34:45 Your-Hostname teamspeak3-server[20126]: token=lcUEBG5YVxnhzPcS5hAmOkW1Zb6KbTZbkntbPFca                                                     
Sep 05 23:34:45 Your-Hostname teamspeak3-server[20126]: ------------------------------------------------------------------
Sep 05 23:34:45 Your-Hostname teamspeak3-server[20126]: 2015-09-05 22:34:45.322567|INFO    |CIDRManager   |   | updated query_ip_whitelist ips: 127.0.0.1,
Sep 05 23:34:45 Your-Hostname teamspeak3-server[20126]: 2015-09-05 22:34:45.323806|INFO    |Query         |   | listening on 0.0.0.0:10011
Sep 05 23:34:53 Your-Hostname systemd[1]: Started TeamSpeak3 Server.
```

其中的 `AccessKey` 即为 `lcUEBG5YVxnhzPcS5hAmOkW1Zb6KbTZbkntbPFca`

此时启动你电脑上的 TeamSpeak 3 客户端。

`Connections -> Connect`

`Server Nickname or Address` 一栏，可以填 IP，也可以填写任何解析到该服务器的域名。
`Nickname` 中写入你的默认管理员昵称。
左下角 `One-Time Privilege Key` 处填入刚刚获得的 `AccessKey`，直接连接即可。

## 后期配置

主界面第四个 Tab 即 `Permissions` 中有详尽的权限组管理。
右键列表中的服务器，可以修改服务器名称。
如果是私有服务器，建议选择 `Edit Virtual Server`，修改密码，并在下方 `Misc` 标签页中取消勾选 `Enable reporting to server list`。



  [1]: https://wiki.archlinux.org/index.php/Arch_User_Repository_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
  [2]: https://wiki.archlinux.org/index.php/AUR_helpers_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#yaourt