---
title: Arch 折腾记 - 为什么我选择 Arch
date: 2018-01-18 02:04:33
tags:
---

*本文引用了大量源自 [ArchWiki](https://wiki.archlinux.org)（含社区翻译的中文版）的内容，若有未标记或声明来源的引用内容，敬请提出。*
**正如标题所指，本文较多为作者个人观点。**<!-- more -->

## 态度

态度决定未来，折腾也是如此。

> *Study hard what interests you the most in the most undisciplined, irreverent and original manner possible.*
> *尽你所能地，以你最不守规矩，最不敬虔，最原始的态度学习你最感兴趣的东西。*
> *---- Richard Feynman (理查德·费曼)*

折腾的基因，在 Arch Linux 中随处可见，也在 Arch 的用户群中无处不在。

> Arch 适用于乐于自己动手的用户，他们愿意花时间阅读文档，解决自己的问题。
> It is targeted at the proficient GNU/Linux user, or anyone with a do-it-yourself attitude who is willing to read the documentation, and solve their own problems.
（源：[中文 ArchWiki][1] / [ArchWiki][2]）

选择 Arch，在某些方面上也是体现了自己的一种折腾精神吧。

## OCD（强迫症）

OCD，即 `Obsessive Compulsive Disorder`，也就是俗称的，强迫症。

之前的 ArchWiki 上的 The Arch Way（Arch 之道）条目中，有一块让我感到了 OCD 的力量，原文已印象模糊，大概是这样的：

> Arch Linux could be called as these:
> Arch Linux
> archlinux
> Arch
> ...
> But **NOT** these:
> ArchLinux
> ArChLiNuX
> ...

显然，就如同在绝大多数（*模糊定义）人忽略
 [《中文排版需求（W3C）》中的中英文字符空格标准][3] 的情况下的一股清流，**Arch 之道**仅仅在名称拼写上，就提出了严格的标准化限制。

## 简单、轻量、强大

大多数主流 Linux 发行版通常会带有一大堆附加工具（软件包），甚至是在服务器操作系统上。
~~（然而往往都是用户用不着的（~~

Arch 不一样，遵循 [KISS 原则][4]。

> 避免任何不必要的添加、修改和复杂增加。
（源：[中文 ArchWiki][5]）

但简洁轻量的同时又附带了所有必须的软件和现代化技术（不包含编译环境，可手动安装软件包），比如 LVM2、软 RAID 支持、udev 支持、以及最新的 Linux 内核（[`core` 仓库中的 `linux` 软件包][6]）。足以保证系统的强大程度不输于其它流行发行版。

## 社区氛围

Arch 有很强的社区氛围在其中，整个系统也是由其开发者 / 贡献者所组成的社区进行开发维护的。

同时，因此，Arch 有一个巨大的官方软件仓库（≈ 46.06GiB，截至 2017/12/05:30 UTC），包含大量的预编译好的二进制软件包。

（不过 Arch 的软件包更新非常激进，基本上是第一时间跟进最新版本，所以难免产生玄学问题）

以及...万能的 [AUR][7]
在其他发行版中，一般情况下，遇到官方仓库没有收录的软件包，就只能自己编译，而部分软件，如果没有编译脚本的话，其编译繁琐程度简直可以上天。

但是 AUR 不一样，相当于一个集中的编译脚本库。结合 Arch Linux 自带的 [Arch Build System][8]，轻松地构建官方软件仓库里没有收录但 AUR 里有对应项目的软件包。

有人说 Arch 是 Geek 玩的，其实，如果你有基本的好奇心和动手能力，那么 Arch 对你来说会有很强的易用性。

除此之外，Arch 社区本身就氛围极佳，同时有自己的一套[社区规范和准则][9]，基本上完善地考虑了社区交流中的各个方面。

## 缺点

Arch 的 `pacman` 软件包管理器着实没有 RHEL / Debian / SUSE 系的软件包管理器成熟，所以 Arch 的滚动更新机制也偶尔会导致一定的系统错误或者整机崩溃。并且没有 `apt` 之类的自动化依赖清除功能，可能留下一大堆依赖残留。

Arch 更新 Linux 内核后需要尽快重新启动，以免出现一大堆玄学报错。

Arch 用来做服务器，仍然远不足 CentOS 之类面向商用服务器的发行版成熟。

...



  [1]: https://wiki.archlinux.org/index.php/Arch_Linux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
  [2]: https://wiki.archlinux.org/index.php/Arch_Linux#User_centrality
  [3]: https://sspai.com/post/33549
  [4]: https://zh.m.wikipedia.org/wiki/KISS%E5%8E%9F%E5%88%99
  [5]: https://wiki.archlinux.org/index.php/Arch_Linux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E7%AE%80%E6%B4%81
  [6]: https://www.archlinux.org/packages/core/x86_64/linux/
  [7]: https://wiki.archlinux.org/index.php/Arch_User_Repository_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
  [8]: https://wiki.archlinux.org/index.php/Arch_Build_System_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
  [9]: https://wiki.archlinux.org/index.php/Code_of_conduct_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)