---
title: '轻松解决 Windows 10 的任务栏 Bug'
date: 2018-03-19 13:45:43
tags:
thumbnail: /pic/taskbar.png
---

# 瞎扯

~~这是一篇除草文。~~<!-- more -->

[据称](https://www.theverge.com/2015/5/7/8568473/windows-10-last-version-of-windows)，Windows 10 将成为 Microsoft 的最后一版 Windows 系统。

从 1985 年 11 月 20 日的 Windows 1.0，到 2017 年 10 月 17 日的 Windows 10 RS3 (1709)，Windows 已经走过了将近 32 个年头。

当然，`Made by Human Beings` 的东西，自然不可能完美。

Windows 10 也因为各种各样的 Bug 而广受诟病。

Windows 10 从 TH1 到 RS3，已经经历了 5 个大版本：

| 版本号 | 版本代号 | 宣传代号 | 发布日期 |
| :----- | :----- | :----- | -----: |
| 1507 | TH1 - Threshold 1 | *（无）* | 2015/07/29 |
| 1511 | TH2 - Threshold 2 | 十一月更新 | 2015/11/10 |
| 1607 | RS1 - Redstone 1 | 周年更新 | 2016/08/02 |
| 1703 | RS2 - Redstone 2 | 创造者更新 | 2017/04/05 |
| 1709 | RS3 - Redstone 3 | 秋季创造者更新 | 2017/10/17 |

本篇文章将告诉你，如何用轻松地方法，对付 Windows 10 任务栏上的常见 Bug.

~~不过根据经验，一般这种 Bug 在低配机器上，在 `硬盘的响应速度过低` 及 `Windows 的辣鸡拥塞算法` 的双重加持下才会频繁出现，高(S)配(S)机(D)器用户应当不咋遇到这些问题。~~

# 正文

## 任务栏全屏不隐藏

很明显是资源管理器睡死了（不

1. 按 `Ctrl` + `Shift` + `Esc`，打开任务管理器。
2. 展开 `详细信息` 。
3. 向下滚动直到 `Windows 进程` 分类。
4. 选中 `Windows 资源管理器`，点击右下角重新启动，搞定。

## 时钟 / 音量 / 网络 / 电源 / 操作中心按钮无反应

事实上，这些模块都不是资源管理器在管理。

而是一个叫做 `Windows Shell Experience 主机` 的系统程序在管理。

资源管理器只负责发送消息给 WSE Host 来让它显示这些界面，所以重启资源管理器并没有什么卵用。

如果你的进程列表中有 `ShellExperienceHost.exe`，尝试用上方同样的方法：

1. 启动任务管理器。
2. 结束掉 `ShellExperienceHost.exe` 进程。

它应该能自动重启，然后你就可以正常使用这些菜单了。

如果没有，那么恭喜你，中奖了。

先尝试**注销并重新登录**，看问题能否解决。

如果不能，再次检查进程列表。

如果依然没有，可能 WSE Host 就莫名其妙丢了。

在 `PowerShell` 中运行如下命令：

`{% raw %}Get-AppXPackage -AllUsers | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"}{% endraw %}`

## 开始菜单按不出

和上面的问题其实是一个原因，只不过是进程不一样而已。

同样的，它叫 `SearchUI.exe`，但它是与 `ShellExperience.exe` 协作工作的，所以一旦你的 WSE Host 挂了，开始菜单肯定会跟着挂。

你只需要终止掉 `SearchUI.exe`，待其自动重启以后应当就能正常打开了。

如果还不行，**注销并重新登录**。

如果都还不行，在更麻烦的情况下，尝试上方的 PowerShell 命令，重新安装全部系统应用。
