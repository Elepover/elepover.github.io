---
title: 利用 SUBST，解决格盘烦恼
date: 2018-01-18 02:02:37
tags:
---

## 引言 ##

> **本文所述操作方法重启后会失效，请通过 Batch 脚本或其他自动化方式执行。**
> **本文所述操作方法不能替代硬件分区。**

刚买来的电脑，或者是刚换的硬盘。
商家有些时候会问：**“分几个区？”**
而可爱的顾(meng)客(xin)有时也总是抢在商家前面：**“分四/五个区！”**<!-- more -->
## 硬盘分区介绍 ##
在日常的使用中，我们遇到的 PC 机，几乎没有分区数 <3 的。（这不是颜文字（雾
我们打个比方，硬盘相当于一个空荡荡的箱子，实际容积有那么大，但是它本身就有一片“加固区域”（分区数据 / 文件表），你放不了东西在里面。
而你需要把各个区域隔开，这样你才能让多个空间互不影响。
你有一本《康熙字典》，
总空间能放进去；
但是隔板太多，每个小空间都放不下···
并且隔板本身也会占用一部分空间。
比如，用到后期，你会发现那种神作：

> 今天我想看看<del>小</del>电影。
> 下载一部看吧，8 GiB。
> 什么？磁盘空间不足？换盘换盘！
> C:
> ■■■■■■■■■□ 90% 1GB 可用
> D:
> ■■■■■■■■□□ 80% 5GB 可用
> E:
> ■■■■■□□□□□ 50% 5GB 可用
> ...
> Excuse me?

但是我们多区合为一区，就不存在那种问题。

具体详见 [Wikipedia](https://zh.wikipedia.org/wiki/分区).

## 什么是 SUBST ##
SUBST 是 Microsoft Windows 自带的**文件夹**映射工具，它能将一个文件夹映射为一个**虚拟驱动器**，与文件夹所属驱动器共享空间，虚拟驱动器的读写权限取决于原文件夹的权限设定。
**再次警告：文件夹映射在重新启动后会失效，**
<del>但我并不是很懂为什么 Micro$hit 不在 EXPLORER 里面添加这个选项。</del>
以下为 *SUBST /?* 的输出 (Microsoft Windows [Version 6.1.7601])：

> 将路径与驱动器号关联。
> 
> SUBST [drive1: [drive2:]path]
> SUBST drive1: /D
> 
>   drive1:        指定要分配路径的虚拟驱动器。
>   [drive2:]path  指定物理驱动器和要分配给虚拟驱动器的路径。
>   /D             删除被替换的(虚拟)驱动器。
> 
> 不带参数键入 SUBST，以显示当前虚拟驱动器的列表。

## 实战 ##
新买到的电脑 / HDD / SSD, 我们可以分两个区就 OK：

> C: - 系统分区 (30%)
> D: - 软件 / 映射用分区 (70%)

系统分区我们一般可以不用动它（墙裂反对文稿数据放在系统分区的行为）。
软件分区，我们准备这样几个文件夹：

> Archives
> Documents
> Downloads
> Images
> Program Files 
> Program Files(x86)
> Temporary
> Videos
> Workspaces
> ...

那么我们可以针对每一个文件夹执行一次映射。

> SUBST E: D:\Archives
> SUBST F: D:\Documents
> SUBST G: D:\Downloads
> SUBST H: D:\Images
> SUBST I: D:\Temporary
> SUBST J: D:\Videos
> SUBST K: D:\Workspaces
> ...

完成以后，你就会发现多出了 7+ 个 “本地磁盘”。
如何重命名呢？谁也不想重新启动以后手动重命名吧。
幸运的是，M$ 提供了 LABEL 指令。
*LABEL* 输出：

> 创建、更改或删除磁盘的卷标。
> 
> LABEL [drive:][label]
> LABEL [/MP] [volume] [label]
> 
>   drive: 指定驱动器号。
>   label  指定卷标。
>   /MP    指定卷应被视为装入点或卷名。
>   volume 指定驱动器号(后面跟一个冒号)、装入点或卷名。如果指定了卷名，/MP 标志则不必要。

也就是说，我们可以组合 SUBST 和 LABEL 指令共同完成自动化的虚拟驱动器映射。
**注意: SUBST 不需要管理员特权, LABEL 需要 Administrators 及其以上的权限。**
我们可以写一个 Batch Script, 保存为 .cmd / .bat 文件运行。
代码:

    @echo off
    rem Code from Elepover's Blog (https://kruin.net)
    rem Copyright (C) 2017 Elepover.
    echo Creating link...
    subst E: D:\Archives
    subst F: D:\Documents
    subst G: D:\Downloads
    subst H: D:\Images
    subst I: D:\Temporary
    subst J: D:\Videos
    subst K: D:\Workspaces
    ...
    echo Linking complete. Renaming...
    label E: Archives
    label F: Documents
    label G: Downloads
    label H: Images
    label I: Temporary
    label J: Videos
    label K: Workspaces
    ...
    echo Renaming complete.
    echo Finished.
    exit

甚至我们可以精简成一行指令：
`cmd /c subst E: D:\Archives | subst F: D:\Documents | subst G: D:\Downloads | subst H: D:\Images | subst I: D:\Temporary | subst J: D:\Videos | subst K: D:\Workspaces | ... | label E: Archives | label F: Documents | label G: Downloads | label H: Images | label I: Temporary | label J: Videos | label K: Workspaces`
（实际上是利用了命令提示符中的 "|" 符号。）

如果使用 VBScript 的话，咱可以实现无窗口执行。
注意必须要给脚本管理员权限或者 Invoker 的权限等级为 Administrators+ 才能实现完美工作。
完毕以后，将脚本文件放在

`%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`

（可直接键入资源管理器地址栏，通用。）
或者是修改注册表达到开机启动的目的。

大概能想到的就这么多了，回见！