---
title: 除草 - 如何干掉修改网的“禁止粘贴”
date: 2019-10-24 11:11:11
tags: F12, chrome, devtools, 批改网, 禁止粘贴
thumbnail: /pic/lemme-ctrl-v.png
---

批改网的确是很好用的作文批改工具，有效分担了教师的负担。但是“禁止粘贴”选项是坏文明，~~为了除草，~~我们来研究以下如何消灭掉它。<!--more-->

# 症状

如果老师启用了“禁止粘贴”选项，在尝试粘贴文本时会出现以下弹窗

![触发的 alert() 弹窗](https://static.elepover.com/blog/pigaiorg/alert.png)

# 分析

在开发者工具中尝试定位文本框对应的元素

![定位元素](https://static.elepover.com/blog/pigaiorg/f12_locate.png)

随即查找对应的 `Event Listener`，我们是在 `Ctrl + V` 时被拦截的，那么应该查找 `keydown` 事件。

![搜索 Event Listener](https://static.elepover.com/blog/pigaiorg/locate_eventlistener.png)

成功定位到 `init_no_paste()` 函数，其包含在页面加载的 `spss.js` 中。

![spss.js](https://static.elepover.com/blog/pigaiorg/spss.js.png)

至此大概可以确定“禁止粘贴”的工作原理了。

- 监听键盘事件，阻止 Ctrl + V 快捷键
- 监听拖放事件，阻止拖放粘贴
- 监听鼠标事件，阻止右键菜单打开

# 解决方案

解决方案从软核到硬核，均测试有效。

## 备用右键菜单

这算是最简单的解决方案了，你不让我打开右键菜单？

不好意思！咱还有备用的。

Windows 请前往右上角`菜单` -> `编辑` -> `粘贴`

macOS 请直接在顶栏`编辑` -> `粘贴`

![Chrome 备用方案](https://static.elepover.com/blog/pigaiorg/backup.png)

![Firefox 备用方案](https://static.elepover.com/blog/pigaiorg/backup_firefox.png)

## 禁用 Event Listener

既然你要监听，那我不让你监听不就完事了。

`按下 F12` -> `定位到 contents 元素` -> 切换到 `Event Listeners` 选项卡 -> 展开 `keydown` 和 `keyup` 事件 -> 依次删除

![删除 Event Listener](https://static.elepover.com/blog/pigaiorg/delete.png)

## 用控制台来粘贴

最硬核的粘贴方法。

`按下 F12` -> 切换到 `Console` 选项卡，在下方键入:

`contents.value = "你想输入的文本";`

保证引号内文本只有一行，换行请使用 `\n` 代替。

# 结束

批改网啊，你已经是个成熟的网站了，该...~~（被打死~~
