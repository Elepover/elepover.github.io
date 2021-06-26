---
title: 当 NVIDIA Optimus 遇见 osu!
date: 2020-05-15 00:00:00
tags:
- nvidia
- optimus
- dual-gpu
- osu
- graphics
thumbnail: /pic/osu.png
---

笔记本，性能和续航，到底要哪一个？

<!-- more -->

## 曾经

- 要性能 → dGPU (Discrete GPU，独立图形处理模块，简称独显) → 续航爆炸
- 要续航 → iGPU (Integrated GPU，集成图形处理模块，简称集显) → 性能不行

渲染流程要么是这样的：

![iGPU route](https://static.elepover.com/blog/optimus/igpu-route.svg)

要么是这样的：

![dGPU route](https://static.elepover.com/blog/optimus/dgpu-route.svg)

~~我&nbsp;&nbsp;全&nbsp;&nbsp;都&nbsp;&nbsp;要？~~

介于此问题，部分 OEM 厂商尝试过在 BIOS 中新增一个“选择 GPU”的选项，此时，渲染流程差不多是这样的：

![Selectable route](https://static.elepover.com/blog/optimus/old-way.svg)

如果要切换 GPU，则需要切换数据选择器状态，要么黑屏一次，要么需要重启系统。

## 现在

于是，2010 年，老黄（NVIDIA）家推出了一种名为 NVIDIA Optimus 的解决方案。具体实现是：

- Optimus Routing Layer (ORL)（操作系统内核级）自动选择 iGPU/dGPU 作为**渲染**芯片
- 由 Optimus Copy Engine (OCE) 将 dGPU 渲染后的帧复制到 iGPU 内存中
- 笔记本自带显示器直连 iGPU，由 iGPU 驱动

在这些情况下，ORL 将自动为应用程序分配 dGPU：

- 任意 DirectX 函数调用
- ~~DXVA（DirectX 视频回放硬件加速）调用~~
- CUDA 调用

> 早期 Intel iGPU 视频解码能力不足，所以调用了 dGPU 来进行视频解码。
> 后期性能提升后，视频解码任务不再转移到 dGPU，而是留给 iGPU 执行。

渲染流程变成了这样：

![Optimus route](https://static.elepover.com/blog/optimus/optimus.svg)

Optimus 技术的好处在于，在不需要 dGPU 时，其可以完全处于关闭状态，由 iGPU 完成运算，降低耗电；而在需要 dGPU 时，可以直接动态切换，不会遇到重启/黑屏等问题。

这样，既保住了 iGPU 的续航功效，又提供了 dGPU 的优秀性能，一举两得。

但真的 Optimus 就完美了吗？并不。

## 问题

Optimus 在大型游戏（GTA V, Watch_Dogs, Forza Horizon 4, Rust, CS:GO）上会自动选择 dGPU 作为渲染芯片，表现优异，独显的性能还是发挥得很充分的，可以达到比较理想的帧率（平均 80 FPS 左右，1920*1080，高画质）。可以说这一套实现效果很不错。

但是至于 osu! 这类对性能要求并不高的游戏呢？

Bad news: 即使选择了 dGPU，仍然显示为 100 FPS 左右，甚至达不到我的显示器刷新率（120Hz）：

![Internal display](https://static.elepover.com/blog/optimus/before.png)

同时，伴有轻微的掉帧（卡顿感）。让 osu! 这种音乐游戏的玩家体验更烂了。

同时 GitHub 上也有用户给开发中的 osu!lazer 发了 issue，请求优化启用 Optimus 的设备上的游戏体验（如 [#5582](https://github.com/ppy/osu/issues/5582), [#8165](https://github.com/ppy/osu/issues/8165)）。

## 调查

通常有三种可能

- osu! 本身优化问题
- iGPU 瓶颈
- OCE 带宽不足

这里要提到，而部分笔记本有一个**特殊的 Optimus 实现**（我的笔记本正是如此）：虽然内置显示器是连接到 iGPU 的，但是笔记本的 HDMI 接口又是直连到 dGPU 的。于是渲染流程变成了这样：

![Optimus alternative](https://static.elepover.com/blog/optimus/optimus-alternative.svg)

其到外置显示器的路程更短，并且意味着我们可以直接排除 iGPU/OCE 瓶颈，确实是否为 osu! 优化问题。

~~Hey Bim,~~ guess what!

~~（龟速船长粉丝可能认识这个梗）~~

结果是：

![External display](https://static.elepover.com/blog/optimus/after.png)

是的，戏剧性的帧率上升，卡顿感也不复存在了。

并且，由于路程的缩短，输出延迟也会小幅度的降低。

## 结论

显然，问题出在了 `渲染好的帧 → 内置显示器` 的路径上。

解决方案？很不幸，暂时没有，因为 iGPU 到内置显示器的连接是硬件级的，无法轻易修改。

这种情况下，瓶颈不在渲染阶段，而是在输出阶段。而大型游戏不容易观测到此类现象的原因，更多的是因为瓶颈在 dGPU，其渲染速度还没有达到输出阶段的瓶颈。

不过，有 G-Sync 的笔记本就不用担心了，因为要实现 G-Sync 的功能，必须是负责渲染的 GPU 直连显示器，有的厂商干脆屏蔽掉了 iGPU。

总体上 Optimus 还是利大于弊的，购买笔记本的时候，如果在这方面有介意，也请务必关注 Optimus 功能。
