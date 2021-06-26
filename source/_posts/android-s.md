---
title: 不使用 TWRP，为你的 Pixel 设备刷入 Android 12 和 Magisk
date: 2021-05-19 00:00:00
tags:
- android
- "android 12"
- "android s"
- google
- pixel
- phone
thumbnail: /pic/android-s.png
---

Android 11 的继任者，Android 12 (S) 已经推出了第一个公共 beta 版，并向 Pixel 等设备开放使用。

但 TWRP 之类的第三方 Recovery 可能还没做好 Android 12 的支持，是不是 Magisk 就无望了呢？其实并不。

<!-- more -->

跟随这篇文章的步骤，你将可以：

- ✓ 刷入最新的 Android 12 Public Beta
- ✓ 在无需第三方 Recovery 的情况下刷入 Magisk

## 0x0 准备工作

你将需要：

- Android Platform Tools
  - [Windows](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)
  - macOS
    - Homebrew: `brew install android-platform-tools`
    - [直接下载](https://dl.google.com/android/repository/platform-tools-latest-darwin.zip)
  - Linux: 直接使用包管理器安装或者[直接下载](https://dl.google.com/android/repository/platform-tools-latest-darwin.zip)
- [Android 12 原厂镜像](https://developer.android.com/about/versions/12/download#factory-images)
- [Magisk 23.0](https://github.com/topjohnwu/Magisk/releases/download/v23.0/Magisk-v23.0.apk) 或更高版本
- 解锁的 Bootloader

> 💡 这篇文章里将如同这样标记我在安装过程中踩的坑以及如何回避。还有一些小提示。
> 
> 个人配置:
> - Pixel 3 (刷入时版本为 Android 11, `RQ2A.210505.002`，2021 年 5 月)
> - macOS Catalina 10.15.7
> - Magisk 23.0
>
> 既然你是 Pixel 用户，我们则假设你已经有一定的基本知识。Platform Tools 安装，控制台交互使用之类内容将不再于此赘述。

> 💡 我们使用直接刷入原厂镜像的方式，不需要在 [Android Beta 计划](https://www.google.com/android/beta#devices)注册即可使用。但你如果希望收到后序版本的 OTA 更新，请在设备上登录你的 Google 账户，并在网页中选择注册你的设备。

当你准备好以上工具后，我们就可以开始了。

## 0x1 解锁 Bootloader

> 💡 如果你的 Bootloader 已经解锁，请暂时不要跳过此节。

### 备份你的数据

**如果你的 Bootloader 还未解锁**，立刻备份你的数据！

在 Bootloader 解锁过程中，所有应用数据、系统设置等将在解锁过程中被彻底抹去，在一切开始之前，请务必备份你的数据。

**如果你的 Bootloader 已经解锁**，立刻备份你的数据！

刷入原厂镜像过程中，数据仍然会被一并抹去，请务必在开始前备份你的数据。

### 退出 Google 账户

> 💡 如果你的 Bootloader 已经解锁，即使 FRP 在默认情况下会自动禁用，但仍然建议你执行这一步骤以防万一。你可以跳过本节的剩余部分。

在 `设置 > 帐号` 中退出你的 Google 账户登录。

### 允许 Bootloader 解锁

在 `设置 > 关于手机` 中滚动到底部，连续点击「版本号」，直到解锁开发者选项。

前往 `设置 > 系统 > 开发者选项`，打开「OEM 解锁」开关。

### 进入 Bootloader

关机或重启，开机时保持按住「音量减」键，直到你看到有红色 ⚠️ 感叹号及红色 "Fastboot mode" 字样时即可松手。

> 💡 如果你选择重启，则不需要按电源键，保持按住「音量减」键即可。
>
> 如果你直接开机，请快速放开电源键，否则可能被识别成「开机」导致直接进入系统。
>
> 当然，如果你启用了 USB 调试，你也可以直接 `adb reboot bootloader`，这是最稳定的方法。

### 解锁

使用 `fastboot flashing unlock` 解锁 Bootloader，并在手机上使用音量键选择「Unlock」，按电源键确认解锁 Bootloader.

> ⚠️ 如果选择确认，你的数据将在这一步被清除，请确认你已经备份了重要数据。

> 💡 部分 Pixel 2 XL 有一个特殊的命令：`fastboot flashing unlock_critical`，但由于 Android 12 并没有对 Pixel 2 系列的官方支持，所有支持的设备均使用上述命令即可。

## 0x2 刷入 Android 12

1. 找到我们刚刚下载的 Android 12 原厂镜像，解压 zip.
   > 💡 如果你使用 Linux 或 macOS，请确定 `flash-all.sh` 有执行权限，若要添加执行权限，请运行 `chmod +x flash-all.sh`
2. [进入 Bootloader](#进入-bootloader).
3. 刷入 Android 12
   - 如果你是 Windows 用户，执行 `flash-all.bat`
   - 如果你是 Linux 或 macOS 用户，执行 `./flash-all.sh`
4. 等待刷入脚本执行完毕

至此，Android 12 刷入完毕。

## 0x3 打补丁

适用于 Pixel 3 的 TWRP 停更在了 3.3.0 且不支持 Android 10 或更高版本，于是直接刷入 Magisk 包的方法就无法使用了。

1. 正常进入系统，正常完成设置
2. [解锁开发者选项](#允许-bootloader-解锁)，打开「USB 调试」
3. 安装下载好的 Magisk 安装包: `adb install Magisk-v23.0.apk`
   > 💡 禁用 Google Play Protect 可以显著提高安装速度。
4. 找到我们刚刚解压的 zip，其中还有一个 zip，名字类似为
   > `image-设备代号-版本号.zip`，如 `image-blueline-spb1.210331.013.zip`
5. 将其解压，找到 `boot.img`
6. 推送到手机: `adb push boot.img /sdcard`
7. 在 Magisk 应用中，选择「Magisk」卡片上的「安装」
8. 选择「选择并修补一个文件」，再选中我们刚刚推送到手机上的 `boot.img`
   > 💡 Magisk 打完补丁后，文件名将有一块随机部分，建议提前改名以便拉取和刷入。
   >
   > ⚠️ 请勿直接复制粘贴以下命令。
9. 待 Magisk 打完补丁后，将此文件从手机中拉至电脑: `adb pull /sdcard/Download/Magisk-xxx.img`
10. 重启到 Bootloader
    > 💡 我们已经用上 ADB 了，跑 `adb reboot bootloader` 即可。
11. **启动**打补丁后的 `boot.img`: `fastboot boot Magisk-xxx.img`
    > ⚠️ Pixel 3, Pixel 5 已确定，其它 Pixel 设备可能同样受影响。
    >
    > 直接刷入 Magisk 修补后的 `boot.img` 将导致无法启动，**请不要使用** `fastboot flash boot Magisk-xxx.img` 直接刷入！

此时你达到了「临时 root」的效果，重启后将失效，接下来我们使它「永久化」。

## 0x4 永久化刷入 Magisk

1. 回到 Magisk 应用
2. 选择「Magisk」卡片上的「安装」
   > 💡 此时，你会发现比起之前多了一个选项，而它恰好就是我们需要的。
3. 选择「直接安装（推荐）」
4. 待 Magisk 刷入完成以后，重启即可

## 0x5 完成

全部工作正式完成！接下来可以恢复数据，正常使用了。

## -0x1 以防万一

如果遇到以下问题：

- 卡在 Google「G」开机动画
- Google logo 出现后直接黑屏重启
- 回到 Bootloader 界面，"Enter reason" 显示 `no valid slot to boot`

那么很不幸，刷入的 `boot.img` 出了问题，若要在不丢失数据的情况下恢复启动，找到我们刚刚解压的原厂 `boot.img`，运行 `fastboot flash boot boot.img`，重启即可。

> 💡 如果遭遇 `no valid slot to boot`，很有可能现在的启动槽位不是当前系统所在槽位，请切换槽位后再刷入 `boot.img`
>
> 原理是：原本的启动槽位启动失败后，自动回落到另一个启动槽位，如果仍然启动失败就会回到 Bootloader，等待救援操作。
>
> 检查 Fastboot 界面显示的「Boot slot」，如果是 `b`，则切换到 `a`，反之则切换到 `b`.
>
> 比如，切换到 `a` 槽位，请使用命令: `fastboot --set-active=a`
