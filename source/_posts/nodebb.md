---
title: Arch 折腾记 - 搭建 NodeBB
date: 2018-01-18 02:04:24
tags:
---

NodeBB 是一个~~搞笑高校~~高效*的论坛系统（辣鸡输入法），由 Node.js 驱动。
（PS 不得不说，现在 JS 真的是越来越万能了）
（连宇宙第一 IDE（Visual Studio）也靠它做后端*）
Official Site: https://nodebb.org
GitHub Page: https://github.com/NodeBB/NodeBB

在接下来的教程中，全程使用 Arch Linux 系统。~~（其他发行版自己看着办）~~
*: 部分内容来自 [NodeBB Documentation](https://docs.nodebb.org/installing/os/arch/)
<!-- more -->
## 环境准备

> 本教程中，全程使用 root 用户。

首先，鉴于 Arch Linux 的激进更新性质，在搭建任何环境之前，如果有条件，请先执行一次**完整的系统备份 / 快照**。
执行系统更新（这个指令会强制更新数据库）：
`pacman -Syyu`

> 默认情况下，如果出现 `==> Generating module dependencies` 字样，请在更新完毕以后重启系统，保证稳定性。

安装依赖：
`pacman -S git nodejs npm redis imagemagick icu nginx`

## 配置数据库

> NodeBB 不支持 MySQL（没压岁钱了）数据库。本教程将使用 Redis 作为 NodeBB 的数据库系统。

**请勿在 Redis 服务器运行期间执行配置。**

设置 Redis 数据库密码（可以使用你自己最喜欢的编辑器）：
`vi /etc/redis.conf`

搜索 `# requirepass foobared`
(是的，Redis 的默认密码）

取消注释（删除 `#` 及其后方空格）
修改 `foobared` 为你自己的新密码，保存退出。
**由于 Redis 为高性能数据库服务器，没有 `fail2ban` 功能，请注意密码安全性。**

设置 Redis 为开机启动，并启动 Redis。
`systemctl enable redis`
`systemctl start redis`

现在 Redis 数据库已经配置完毕了。

## 安装

进入你安装 NodeBB 的文件夹：
`cd /home`

克隆 NodeBB Git 仓库。
`git clone -b v1.6.x https://github.com/NodeBB/NodeBB.git nodebb`

进入 NodeBB 文件夹，启动 NPM 的安装过程。
`cd nodebb`
`npm install`

启动 NodeBB 安装。
`chmod +x ./nodebb`
`./nodebb setup`

根据屏幕提示依次配置数据库和其他配置。
使用默认配置，请按回车。
建议修改默认端口。

## 创建服务

为 NodeBB 创建 systemd 服务，以便开机启动。
修改以下文件：
`vi /usr/lib/systemd/system/nodebb.service`

填入以下内容：
```
[Unit]
Description=NodeBB Daemon Service
After=network.target
Wants=network.target

[Service]
Type=simple
PIDFile=/var/run/nodebb.pid
ExecStart=/home/nodebb/nodebb dev
ExecReload=/bin/kill -HUP $MAINPID
KillMode=mixed
KillSignal=SIGTERM
Restart=always

[Install]
WantedBy=multi-user.target
```

保存退出，重载 systemd 服务，启动 NodeBB 服务并设置开机启动。
`systemctl daemon-reload`
`systemctl enable nodebb`
`systemctl start nodebb`

## 配置 NGINX 反向代理

在 NGINX 配置文件中填入以下内容。
**（请注意修改配置文件中的域名、端口、SSL 证书 + 私钥文件名。）**

启用并强制使用 HTTPS（已启用 HSTS）：
```
server
{
        listen 80;
        listen [::]:80;
        server_tokens off;
        server_name yourdomain.com;

        return 301 https://yourdomain.com$request_uri;
}
server
{
        listen 443 ssl http2;
        listen [::]:443 ssl http2;
        server_tokens off;
        server_name yourdomain.com;
        ssl_certificate /home/yourcert.crt;
        ssl_certificate_key /home/yourpkey.key;
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
        location / {
                proxy_redirect off;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto https;
                proxy_pass http://localhost:<port>;
                proxy_set_header Host "localhost";
                proxy_set_header Accept-Encoding "";
                proxy_set_header User-Agent $http_user_agent;
        }
}
```

非 HTTPS，由于不推荐，请自行配置。

启动 NGINX 并设置 NGINX 开机启动：
`systemctl enable nginx`
`systemctl start nginx`

## 结束

如果配置均正确，现在你已经可以访问自己的 forum 了！
（也可以重新启动系统以检查其他地方是否出错。）
配置实例地址: https://forum.elepover.com
