---
title: Arch 折腾记 - 搭建 StackEdit
date: 2018-01-18 02:04:16
tags:
---

# Arch Linux 折腾记系列

Arch Linux - A simple, lightweight distribution.

Arch Linux 以其轻量化的特性、易用的编译系统、丰富的社区资源、Geek 化的操作特性著称。

但 Arch Linux 属于小众操作系统，现存的诸多教程多多少少由于包管理器的原因不能与 Arch Linux 通用。当然，有一些教程只需要改一改包管理器的命令就可以了。

本系列文章将记(chui)录(bi)一系列 Arch Linux 的折腾日志。<!-- more -->

# StackEdit

StackEdit 为一个轻量化的浏览器内 MarkDown 编辑器。载入后可离线工作，保存到浏览器中，并以各种文件格式导出。基于 Node.js.

本文部分内容来自 [ArchWiki](https://wiki.archlinux.org) 以及 [StackEdit Wiki](https://github.com/benweet/stackedit/wiki/Install-Debian-8)。

# 开始

**本教程中所有操作均使用 `root` 权限执行。**

首先更新系统：

`pacman -Syu`

安装依赖项：

`pacman -S git npm nodejs nginx`

_* `nginx` 为可选项_

按 Y 回车后执行操作。

安装完毕后，更新 npm 并使用 npm 安装必要的组件：

`npm install npm -g`
`npm install --global gulp`
`npm install --global bower`

选择一个你希望用来安装 StackEdit 的文件夹。

比如 `/root/stackedit`

`cd /root`

随后下载最新的 StackEdit.

`git clone https://github.com/benweet/stackedit.git stackedit`
`cd stackedit`

使用 npm 和 bower 安装 StackEdit

`npm install`
`bower install --allow-root`

修改部分配置：

使用你最喜欢的文本编辑器：

`nano /root/stackedit/public/res/constants.js`

StackEdit 需要守护进程（服务）才能保持运行，否则无法访问。

使用你最喜欢的文本编辑器创建服务：

`nano /usr/lib/systemd/system/stackedit.service`

定位到第四行，输入你的服务器域名：

`constants.MAIN_URL = "https://yoursite.com";`

保存退出。

```
Description=Stackedit daemon

[Service]
ExecStart=/usr/bin/node /root/stackedit/server.js
Restart=always

[Install]
WantedBy=multi-user.target
```

重新载入 systemd services, 设置开机启动并启动 StackEdit 服务。

```
systemctl daemon-reload
systemctl enable stackedit
systemctl start stackedit
```

检查服务状态

```
systemctl status stackedit
```
（按 Q 退出）

如果是 Active 并有如下提示：

`node[197]: HTTP server started: http://localhost:3000`

这说明 StackEdit 服务已经成功启动了。
如果失败，请检查 3000 端口的占用状态。

此时我们已经可以通过服务器访问 StackEdit 了。

现在我们需要 nginx 作反向代理，以便我们通过服务器来访问 StackEdit.

大致数据路线：

`User <---> nginx <---(local proxy)---> nodejs <---> StackEdit`

示例 nginx 配置文件（已启用 HTTPS）：

```
server
{
        listen 80;
        listen [::]:80;
        server_tokens off;
        server_name yoursite.com;
        return 301 https://yoursite.com$request_uri;
}
server
{
        listen 443 ssl http2;
        listen [::]:443 ssl http2;
        server_tokens off;
        server_name yoursite.com;
        ssl_certificate /home/yourcert.crt;
        ssl_certificate_key /home/yourcert.key;
        location / {
                proxy_redirect off;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
                proxy_pass http://localhost:3000;
                proxy_set_header Host "localhost";
                proxy_set_header Accept-Encoding "";
                proxy_set_header User-Agent $http_user_agent;
        }
}
```

未启用的 HTTPS 的 nginx 配置文件：

```
server
{
        listen 80;
        listen [::]:80;
        server_tokens off;
        server_name yoursite.com;
        location / {
                proxy_redirect off;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
                proxy_pass http://localhost:3000;
                proxy_set_header Host "localhost";
                proxy_set_header Accept-Encoding "";
                proxy_set_header User-Agent $http_user_agent;
        }
}
```

完成后，重新启动 nginx.

`systemctl restart nginx`

设置到你自己域名的 A/AAAA 记录，待 DNS 缓存刷新后，直接访问。你已经成功了！

此时大多数的分享和外部导出功能均处于不可用的状态，你需要自己申请 API Key 并在如下文件中设置：

`/root/stackedit/public/res/constants.js`

保存后，重新启动 StackEdit 服务：

`systemctl restart stackedit`

And then you're done!