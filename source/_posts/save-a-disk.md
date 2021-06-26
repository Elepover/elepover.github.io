---
title: 拯救一块硬盘的正确姿势
date: 2018-01-18 02:03:27
tags:
---

DiskGenius 启动时会跳出一句话：

> “数据无价，谨慎操作。”

某日，博主的同学 ××× 暴力热插拔我的 SD 卡，导致分区表直接报废...
Windows: 使用此光盘之前需要将其格式化。
~~（光盘你大爷啊光盘！）~~
当然 Ubuntu 是直接挂载失败...
以下是我如何救活这张 SanDisk SD 卡的。<!-- more -->

# 需要的条件

 - 一台安装有主流 Linux 发行版的计算机（我的是 Ubuntu）
 - 一个读卡器（很便宜，街边几块钱一个）
 - 一个大小足够的临时存储设备
 - 网络连接（

# 步骤
首先把待修复的 SD 卡插入读卡器，连接电脑。
注意：此时电脑正在运行的应当是**“主流 Linux 发行版”**。
如果你没有安装 gparted, 使用以下命令:
Ubuntu / Debian:
`sudo apt update && yes | sudo apt install gpart gparted`
Arch Linux:
`sudo yes | pacman -S gpart gparted`
CentOS / RH 系
`yes | yum install gpart gparted`
不同发行版的 gparted 软件包包名不知是否一致，如果有误还请读者指出。

使用 root 权限运行 gparted
`sudo gparted`
在 gparted 启动后，在右上角的设备选择列表中，找到你损坏的 SD 卡（看中部存储空间大小就可确定）。确定路径。
我得到的路径是:
设备:
`/dev/sdc`
分区 1:
`/dev/sdc1`
因为我的文件系统为 FAT32(Win95)
所以执行对应命令:
`sudo dosfsck -a -l /dev/sdc1`
注意需要在执行之前安装 `dosfstools` 软件包，方法同上，不再赘述。

GG,
`dosfsck: Logical sector size is zero.`

_如果 dosfsck 已经成功运行并修复了需要修复的设备，放心地 eject 就好。_

看上去 dosfsck 救不了这张卡了，放弃。
那么尝试一下 gparted 自带的数据恢复功能。
选中待测的 SD 卡，然后选择顶部工具栏中的 `Attempt data recovery`
gparted 会卡住，耐心等候。
`gparted: Could not find any filesystem on the device.`
（无法找到任何文件系统）
卧槽？
卧槽？？？
_如果 gparted 恢复成功，照办就好。_

好了，两种方法均以失败告终。
可是，咱们的大杀器还没出来哟。
**TestDisk** 神器上线！

使用同样的方法，安装 `testdisk` 软件包。
Ubuntu / Debian:
`sudo apt update && yes | sudo apt install testdisk`
Arch Linux:
`sudo yes | pacman -S testdisk`
CentOS / RH 系:
`yes | yum install testdisk`

运行如下命令:
`testdisk /dev/sdc`
打开就有一种高大上的感觉有没有！
接下来，一路回车。
TestDisk 的默认选项一般都是最准确的。

![打开 TestDisk 后的界面][1]

打开 TestDisk 后的界面。
(在此我不得不拿我的 U 盘开刀 QAQ)
方向键上下选好分区，**回车继续**。

![第二步][2]

TestDisk 默认为我们选择 `Intel` 类型的分区表。**回车继续**。

![第三步][3]

默认选取 Analyse 选项，**回车继续**。

![第四步][4]

依然选取默认选项（快速扫描），**回车继续**。

![第五步][5]

扫描 ing...

![第六步][6]

扫描结束，得到了以上结果。
_如果你选择的分区表类型为 `None` 则不会允许**回车继续**_

选中正确的分区，按 `P` 列出所有扫描到的文件。
如果你看到的文件名称全部正确，多半都是成功的！
是的，你的文件被恢复了！

> 使用鼠标滚轮 / 方向键 / PgUp / PgDn 在文件列表中导航。
> 按 **方向键右** 更改文件夹 (`..` 为上级文件夹)
> 按 **h** 隐藏已删除的文件（它们多数遭到了覆盖，所以恢复出来也几乎无法正常读取。）
> 按 **a** 选择所有文件。
> 按 **C** (**Shift + c**) 复制选择的所有文件。
> 按 **:** (**Shift + ;**)选择当前文件。
> 按 **c** 复制当前文件。

我的操作顺序:
h -> a -> Shift + c
随后选择正确的文件夹，存放恢复出的文件。
按下 **Shift + c**, 将选中的文件恢复到当前选择的目录中。
按下 **Shift + q**, 重新选择文件。

随后它会自动复制出所有选择的文件。
`Copying, please wait... 0 ok, 0 failed`
复制完成后会回到选择文件的界面。
还在等什么？一路按 q 退出啊~~(某 13: 你 ^C 会死么)~~
你选择的所有文件都已被安全复制到外部存储设备中了。
如果不放心，还可以随手打开几个文件检查一下。
打不开就删，因为那是无法恢复的文件，强行恢复出来当然有所损坏。
现在，你可以安全地格式化被恢复的损坏设备了。
使用 nautilus 或其他文件管理器，将其格式化。
把复制出来的文件又复制回去，正所谓落叶归根（雾）。
所有工作都搞定啦！

# 碎碎念
开头我已经说过

> “数据无价，谨慎操作。”

遇到文件系统损坏的时候:

**千万不要执行任何写入操作，立刻尝试恢复数据！！！**
**千万不要执行任何写入操作，立刻尝试恢复数据！！！**
**千万不要执行任何写入操作，立刻尝试恢复数据！！！**

重要的事情说三遍。
因为你一旦执行任何写入操作，就很有可能覆盖掉你原有的数据，导致文件损坏。
作为使用者，第一时间尝试恢复数据，而不是听 Windows 怂恿，无脑格式化了再尝试恢复数据。
虽说不是不可以恢复，但是凭空增加了数据恢复的难度，并且极有可能损坏你的现有文件~~（甚至是范围伤害）~~。

日常使用的时候:

**一定记得「安全弹出」！**
**一定记得「安全弹出」！**
**一定记得「安全弹出」！**

重要的事情说三遍。
千万不要图方便无脑拔 U 盘 / 其他存储设备。因为你不知道有没有任何程序正在使用这个存储设备。甚至是操作系统本身。
~~（比如 Linux 日常拿外部设备当缓存）~~
嘛，这样的后果就是，要么产生删不掉的文件(夹)之类的玄学问题，要么导致文件损坏，要么损坏整个文件系统。
我恢复的那张 SD 卡，因为热插拔，导致了 **3% 的文件损坏**（范围伤害）。好在，没有什么重要的东西丢失。
在刚刚的截图中也看到了俩 warning.

良好的移动存储设备使用习惯是保证数据安全的重要要求。
大概就这样。

本文终。







（期末考砸了，心累 QAQ）


  [1]: https://static.elepover.com/blog/testdisk/1.webp
  [2]: https://static.elepover.com/blog/testdisk/2.webp
  [3]: https://static.elepover.com/blog/testdisk/3.webp
  [4]: https://static.elepover.com/blog/testdisk/4.webp
  [5]: https://static.elepover.com/blog/testdisk/5.webp
  [6]: https://static.elepover.com/blog/testdisk/6.webp
