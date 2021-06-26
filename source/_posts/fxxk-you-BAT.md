---
title: 如何科学地发送一个扣扣分享
date: 2018-01-18 02:01:29
tags:
---

扣扣在执行某些分享的适合是打开一个链接，而根据传递的 Query，可以得出一个固定格式：
<!-- more -->
<a target="_blank" href="http://connect.qq.com/widget/shareqq/index.html?site=<siteName>&title=<shareTitle>&summary=<contents>&pics=<picURL>&url=<target>">http://connect.qq.com/widget/shareqq/index.html?site=<siteName>&title=<shareTitle>&summary=<contents>&pics=<picURL>&url=<target></a>

其中各参数含义如下：

```
 - <siteName> 站点名称
 - <shareTitle> 分享标题
 - <contents> 简介内容
 - <picURL> 封面图片地址
 - <target> 分享链接（点击分享打开的将是此链接）
```
 
注意：分享成功后，会根据所提供的 *<target>* 进行分析，可能覆盖掉原先的内容。

Example:
<a target="_blank" href="http://connect.qq.com/widget/shareqq/index.html?site=Elepover&title=%E5%A6%82%E4%BD%95%E7%A7%91%E5%AD%A6%E5%9C%B0%E5%8F%91%E9%80%81%E4%B8%80%E4%B8%AA%E6%89%A3%E6%89%A3%E5%88%86%E4%BA%AB&summary=%E6%89%A3%E6%89%A3%E5%9C%A8%E6%89%A7%E8%A1%8C%E6%9F%90%E4%BA%9B%E5%88%86%E4%BA%AB%E7%9A%84%E9%80%82%E5%90%88%E6%98%AF%E6%89%93%E5%BC%80%E4%B8%80%E4%B8%AA%E9%93%BE%E6%8E%A5...&pics=https://blog.elepover.com/favicon-blanked.png&url=https://blog.elepover.com/archives/18.html">将此文章分享给扣扣好友</a>