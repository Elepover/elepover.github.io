---
title: 简单粗暴的一种映像劫持
date: 2018-01-18 02:02:55
tags:
- 映像劫持
---

## 什么是映像劫持
中文维基上**貌似没有**找到映像劫持的相应条目，简单解释一下好了。
当你启用一个文件名为 *A.exe* 的应用程序的时候，由于映像劫持，系统会自动启动 *B.exe*，**而非** *A.exe*.
而**包括百度在内的**好些病毒正是运用了映像劫持的方法，搞死了不少杀毒软件和其他程序。

<!-- more -->

## 如何做到
实际上这类映像劫持非常简易，只需要通过简单地修改一下注册表。
**注意：请谨慎操作注册表，由于设置不当导致的系统不稳定或工作异常，作者概不负责。**
> 启动注册表编辑器（按下 **Win+R** ，输入 **regedit**，通过 UAC。
> 定位到 `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options`
> 右键点击 `Image File Execution Options` 项，**选择 "新建 -> 项"** 。
> 重命名项为 **需要劫持的应用程序文件名** 。
> 再次右键重命名后的项，**选择 "新建 -> 字符串值"**。
> 重命名为 `Debugger`。
> 双击 `Debugger` 项，修改值为 **带引号的** 新程序路径。

## 你是在哪看到的
有一点经验的程序员应该都用过 [Sysinternals Process Explorer][1] 吧。
大概长这样：
![本图亮点较多][2]

这货的强大程度逆天，**进程树、进程线程 + DLL 控制、GPU 图表、访问权限**等等...
神 · 应有尽有。
这玩意对我来说最大的用处是...

> 首先，学校电脑上有控制软件。
> 其次，这软件进程防杀。
> 再其次，我需要干掉它...

于是乎...无聊下载了个 Process Explorer，看有没有什么入口点。
还 · 真 · 有。
以下是终结它的方法：

> 右键进程，**Suspend**（挂起）。好了，已经解决了。（防杀进程不防挂进程 23333 ）
> 双击进程，找到 **Thread**（线程）一栏，随便找到几个线程 Kill 掉，自行 FC 崩溃。

扯远了...
于是我决定用它来当作任务管理器。
结果咱的 *Mark Russinovich* 先生（致敬）已经考虑到了。
选择 Options 工具栏项，就会有一个 **"Replace Task Manager"** 的选项。
打勾，通过 UAC，渣渣 Windows 任务管理器去死吧！（雾
然后各种方法启动 `taskmgr.exe` 都会启动 Process Explorer...
我起先以为是替换了系统文件，但是想一想，Sysinternals 还会做出这种智障的做法么...
于是去看了看，果然，`taskmgr.exe` 老老实实地躺在那，风雨不动安如山，呜呼...（大雾
很好奇它是如何做到的...
直到有一天我想分析分析 Process Explorer 自身...
双击打开属性，我看到了个 Autostart Location...
`HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\taskmgr.exe\Debugger`
然后我按了一下 Explore ...
![城会玩][3]

Done!

  [1]: https://technet.microsoft.com/en-us/sysinternals/processexplorer.aspx
  [2]: https://static.elepover.com/blog/procexp.webp
  [3]: https://static.elepover.com/blog/regedit-procexp.webp
  [4]: https://static.elepover.com/blog/chrome-ie.gif