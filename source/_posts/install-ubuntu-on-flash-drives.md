---
title: 将 Ubuntu 16.04 LTS 安装至 U 盘上 & 心得
date: 2018-01-18 02:01:50
tags:
---

**此文章已有两年未更新，部分内容可能不再适用**

# 准备步骤 / 工具

> Ubuntu 16.04 LTS ISO 镜像文件。
下载链接：https://www.ubuntu.com/download/desktop/thank-you?country=&lt;Region&gt;&amp;version=16.04.1&amp;architecture=&lt;Arch&gt;
&lt;Region&gt;: 区域代码，比如 US 或 CN。
&lt;Arch&gt;: 处理器架构，比如 i386 或 amd64。<!-- more -->
<a href="https://www.ubuntu.com/download/desktop/thank-you?country=US&amp;version=16.04.1&amp;architecture=i386" target="_blank">x86 通用链接</a>
<a href="https://www.ubuntu.com/download/desktop/thank-you?country=US&amp;version=16.04.1&amp;architecture=amd64" target="_blank">x64 通用链接</a>
上方为官方源，自动选择镜像。
BTW: 国内推荐使用 <a href="https://mirrors.ustc.edu.cn/ubuntu-releases/16.04/" target="_blank">ustc mirror.</a>
可在其中寻找适合自己系统配置的镜像文件。
> PendriveLinux Installer。
下载链接：[https://www.pendrivelinux.com/downloads/Universal-USB-Installer/Universal-USB-Installer-1.9.7.0.exe](https://www.pendrivelinux.com/downloads/Universal-USB-Installer/Universal-USB-Installer-1.9.7.0.exe)
> 一个足够大的 U 盘。（我的是 64 GB）

# 提前提示

> 1.  PendriveLinux Installer 不像其他工具，不会格式化 目标 U 盘，但仍然请做好备份工作！
> 2.  不同的 Ubuntu Mirror 的网速和断点续传支持不一致，如果不爽请重新开一次直链或者使用 ustc mirror（
> 3.  PendriveLinux Installer 只是创建了一个 ISO 对应的 U 盘，所以这样启动的 U 盘虽然有数据保存（前提是你打开了缓存文件，会生成一个 casper-rw 文件在 U 盘根目录。），但是却会存在 "Live session user"、"安装 Ubuntu 16.04 LTS" 等等非安装版 Ubuntu 的功能存在。
> 4.  请谨慎使用并定期备份 casper-rw 这个文件，否则遇到断电极有可能无法挂载 root 类内核问题导致无法开机（亲身经历）。

# 步骤

> 1.  首先，打开 PendriveLinux Installer，选中你的 U 盘，选中 ISO 文件。
> 2.  随后调节 casper-rw 文件大小（推荐 16GB+，只要你 U 盘够大（前提是 exFAT 等文件系统，FAT32 最高允许单文件 4GB），因为这会当你 Ubuntu 的内存空间使用。）
> 3.  然后开始，警告自阅，待其处理。
> 4.  首先它会写卷标（更改为 UUI）和引导数据，然后解压 ISO（内置 7-Zip）。
> 5.  完成后开始创建 casper-rw 文件，耗时较长。（想看进度的可以去资源管理器里面按刷新按着玩）
> 6.  随后自动复制版权文件（可据日志删除），一切完成，重启电脑，选择 U 盘作为启动设备。
> 7.  开机，直接选择 *Try Ubuntu without Inst* 。
> 8.  完成！

# Ubuntu 个性化

### 下载源修改：

> 1.  为了不忍受龟速的下载速度，先进入系统设置（右上角电源图标 &gt; *System Settings*）
> 2.  选择 *Software &amp; Updates* 。
> 3.  在新窗口中 *Download From* 下拉条里选择 *Other Sites *。
> 4.  寻找 *China &gt; mirrors.aliyun.com &gt; OK*
> 5.  当然也可以按右上角，让 Ubuntu 帮你寻找最佳服务器。
> 6.  完成后关闭设置，会提醒你更新软件包列表，更新。
> 7.  完成！

Edit 03/14/17:
作为一个 ustc 的硬粉（
已经**不推荐使用** 嗄狸云 mirror.
科大官方镜像更换教程在此:
https://mirrors.ustc.edu.cn/help/ubuntu.html
此处不再赘述。

### 语言修改：

> 1.  进入设置
> 2.  进入 Language Support
> 3.  待其自动更新，提示你丢失语言项目，跳过。
> 4.  找到列表，把 "汉语（中国）" 拉到顶部，点 Apply To System。
> 5.  随后选择区域格式选项卡，更改为 "汉语（中国）"，点 Apply To System。
> 6.  关闭窗口。
> **注意：Ubuntu 会提示你注销后语言生效，但是请先前往 设置 &gt; Accounts 并创建你自己的账户，否则除了重启，无法再次登录！**

### 修改为 Ubuntu Kylin 主题

Ubuntu Kylin 的主题确实很多方面好看很多（个人认为）

修改步骤如下：
> 1.  打开终端。
> 2.  **请注意你是否使用国内镜像，否则速度会非常感人！**
> 3.  输入 sudo apt-get update && apt-get install ubuntukylin-theme unity-tweak-tool
> 4.  按 Y 并回车确认，待其自动安装。
> 5.  安装完毕后，输入 sudo unity-tweak-tool
> 6.  选择 "主题"。
> 7.  选择 "Ubuntukylin-theme"。
> 8.  前往 "图标" 选项卡，选择 "Ubuntukylin-icon-theme"。
> 9.  完成！
> 随同安装的 Unity Tweak Tool 有相当多的功能，您可以自行探索。

# 使用注意 &amp; 限制

> 1.  在使用 Ubuntu 之前，请**务必确认目标 U 盘的 4K 性能**（不知道的话请使用 HDTune 之类的工具测试一下），4K 性能过低的 U 盘会导致**严重的系统延迟和卡顿**。（本人设备为 DTSE9 G2 64GB，推荐 SanDisk CZ80/CZ88/CZ880 固态闪存盘）
> 2.  如果出现内核问题开不了机，**别慌着重做 U 盘**，先进 Windows 把 casper-rw 文件复制出来，再重做（**重做时记得分配大小大于零的 casper-rw 文件，不然不认**）；重做成功以后**把 casper-rw 文件复制回来**，完美保留数据。
> 3.  Ubuntu 对 设备 的随机存取性能要求较高，建议使用 响应时间低于 2ms 的闪存设备。
> 4.  消灭 "安装 Ubuntu 16.04 LTS" 图标，只需要找到包名，用 apt-get 卸载即可。