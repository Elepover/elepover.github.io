---
title: 结合 TWRP + Magisk，在大多数设备上轻松绕过 Google FRP 和设置向导
date: 2018-06-16 09:43:13
tags:
- twrp
- frp
- root
- recovery
- bypass
- magisk
- setupwizard
thumbnail: /pic/bypass-frp.png
---

本文章将指导您绕过设备上的出厂设置保护。
<!-- more -->

**懒人请直接点[这里](#解决)。**

**本文不适合没有刷机基础知识的用户。**

**作者不对滥用此方法导致的任何后果承担任何直接或间接责任。**

# 前言

博主最近拿到了一部红米 Note5（a.k.a.: Redmi Note 5 Pro (India), codename `whyred`)

拿到“特殊”小米账号，直接解锁 Bootloader 一把梭。

使用 Fastboot 刷入 TWRP Recovery，重启到 Recovery，双清，刷包，RR -> GApps -> Magisk 一把梭。

然而这个版本的 RR 却不尽人意：OTG 无法识别设备，花式睡死...

于是几天后博主又决定回 MIUI Global 养老。

双清，刷包，重启，完成设置向导。

然后我没想到：Google FRP 被触发了。

（`FRP` 即 `Factory Reset Protection`，该功能于 Android L 引入，目的是防止手机遗失后被他人使用。）

>! Not signed in
>
>To use this phone, you first have to sign in to an owner's Google Account in use on this phone when it was last reset.
>
>Because you haven't done so, you have to set up the phone again.
>
>[SET UP PHONE]

瞬间气哭.jpg

网上各种 “Bypassing Google FRP” 的方法，基本上都是：

1. 设置向导里面，用各种方式进入浏览器
2. 花式打开 Google App
3. 进入设置。

然而 MIUI 也没有什么辅助功能设置啥的（差评），于是我想到了删除设置向导。

# 解决

然后怎么办呢？

按照旧的方法（Pwr + VolUp）重启进入 Recovery，却发现 Recovery 被替换为 MIUI 的了？！

~~（黑人问号）~~

## 刷回 Recovery

并不是啥大事，让手机进入 fastboot，键入如下命令。

```
fastboot flash twrp.img
fastboot boot twrp.img
```

## 保护我们的 TWRP

进入 TWRP 以后，选择 `Mount` 菜单，挂载 `System` 分区。

回退到主菜单，进入 `Advanced` 菜单，选择 `File Manager`.

进入这个文件夹：

`/system/bin/`

选中名为 `install-recovery.sh` 的文件，重命名为 `install-recovery.sh.bak`（当然，也可以删除，随你喜欢。）

现在，系统就没有办法在开机的时候覆盖掉我们可爱的 TWRP 了。

## 干掉设置向导

送走那玩意以后，回到上级文件夹，再进入：

`/system/priv-app/SetupWizard/`

重命名 `SetupWizard.apk` 为 `SetupWizard.apk.bak`.

**请同时刷入 Magisk，否则无法进行下一步操作。**

重新启动系统。

## 修改设置

进入系统后，我们可以发现，此时，由于设置向导被我们 K 掉了，状态栏不能下拉（AOSP），多任务键和主页键无效。

现在我们来解决这个问题。

我们需要安装一个 `Terminal Emulator`（终端模拟器）。

在 [APKMirror](https://www.apkmirror.com/apk/jack-palevich/terminal-emulator/terminal-emulator-1-0-70-release/terminal-emulator-1-0-70-android-apk-download/download/) 可以找到对应安装包。

进入终端模拟器，输入如下命令：

`su`

确定 Magisk 弹出的授权申请。

再依序执行以下命令：

```
setenforce 0
settings put secure user_setup_complete 1
settings put global device_provisioned 1
setenforce 1
reboot
```

执行完最后一条命令后，手机会黑屏重启。

如果你没有强迫症，那就可以继续用下去了。

强迫症患者请先按住 VolUp，进入 Recovery，把刚刚改掉的 `SetupWizard.apk.bak` 改回来。

至此，我们已经完成全部操作了。

Enjoy your journey!