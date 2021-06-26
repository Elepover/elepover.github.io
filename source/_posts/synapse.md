---
title: Arch 折腾记 - 搭建 Synapse
date: 2018-01-18 02:14:47
tags:
---

## Matrix, Synapse 和 Riot 的关系
> [Matrix](https://matrix.org): An open network for secure, decentralized communication.

Matrix 作为一种即时通讯协议，类似于 Tox，只提供 SDK，不提供官方客户端（原 Matrix Console 已弃用）。运作方式不同（P2P /中心服务器）。

[Synapse](https://github.com/matrix-org/synapse): Matrix 官方服务器核心。
[Riot](https://github.com/vector-im/riot-web): 由 Vector Creations Ltd. 开发的基于 Matrix 协议的 IM 应用。

## 上手安装

Matrix 官方已经在 GitHub 上有非常详尽的教程，不过我们还有更复杂的配置。<!-- more -->

首先运行一次系统更新，保证所有软件包都是最新版本:
`pacman -Syyu`

> 同上集 Arch 折腾记：如发生内核更新，请记得重启系统。

获取依赖包:
`pacman -S base-devel python2 python-pip python-setuptools python-virtualenv sqlite3`

提示组安装，按回车默认安装编译包内所有软件包。

新建 Synapse 运行文件夹:
`mkdir /home/synapse`

切换环境到 Synapse 运行文件夹，使用 pip 执行安装。

`cd /home/synapse`
`virtualenv -p python2.7 ./`
`source ./bin/activate`
`pip install --upgrade pip`
`pip install --upgrade setuptools`
`pip install https://github.com/matrix-org/synapse/tarball/master`

至此，Synapse 已经安装完毕了。

## 后期配置

执行以下指令以初始化服务器:
`python -m synapse.app.homeserver --server-name localhost --config-path homeserver.yaml --generate-config --report-stats=no`

等待执行完毕。
执行以下指令启动服务器:
`synctl start`

注册管理员用户:
`register_new_matrix_user -c homeserver.yaml`
输入用户名，回车；
设置密码，回车；
确认密码，回车；
输入 `yes` 后回车。

等待服务器处理注册请求。

如需开机启动，可以写一个脚本:
```
cd /home/synapse
source ./bin/activate
synctl start
```
设置脚本开机运行一次即可。

## 配置 NGINX 反向代理:

在 NGINX 配置文件中填入以下内容:

```
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    ssl_certificate /etc/ssl/certificate.crt;
    ssl_certificate_key /etc/ssl/server.key;
    server_name yourdomain.com;
    location =/ {
        return 403;
    }
    location /_matrix {
        proxy_pass http://localhost:8008;
        proxy_set_header X-Forwarded-For $remote_addr;
    }
}
```

重新启动 NGINX 以使配置文件生效。

## 完成

现在，尝试使用客户端登录服务器吧。

> 已在 Oct 28 更新注册启用功能。

可用的客户端（Riot）：
PC Desktop: https://riot.im/desktop.html
Android: https://play.google.com/store/apps/details?id=im.vector.alpha
iOS: https://itunes.apple.com/us/app/vector.im/id1083446067

## 版权
本文绝大多数内容参考自 [Matrix 官方文档](https://github.com/matrix-org/synapse/blob/master/README.rst)，仅作了翻译工作和部分修改。请尊重原作者版权。

## 更新
### Oct 17, 2017
（仅针对 Arch 用户）
更新 `libsodium` 包后, Synapse 会无法启动。
#### 报错消息
`libsodium.so.18: cannot open shared object file: No such file or directory`
#### 原因
`libsodium` 库文件发生变动。

#### 解决方法
概览：使用软链接连接新的 `libsodium` 库和原先配置的 `libsodium` 库。

首先从报错消息中得到原来的库文件名。
上方报错消息中原库文件名为: 
`libsodium.so.18`

继续，搜索新的 `libsodium` 库文件: 
`find /usr/bin | grep libsodium.so`
得到最新的 `libsodium.so`
例如: `libsodium.so.20`

随后创建软链接: 
`ln -s /usr/bin/libsodium.so.20  /usr/lib/libsodium.so.18`

进入 Synapse 配置文件所在文件夹，重新启动 Synapse:
`source bin/activate && synctl start`

应该就不会报错了吧（

### Oct 28, 2017
#### 启用注册功能

进入 Synapse 安装文件夹，修改配置:
`cd /home/synapse && vi homeserver.yaml`

搜索如下内容:
`enable_registration`

修改为: `enable_registration: true`

考虑到安全，请尽可能启用 reCAPTCHA。具体可在 [Google](https://www.google.com/recaptcha) 获取。
获得到的 Site Key 填入 `recaptcha_public_key` 字段中。
同样，获得的 Secret Key 填入 `recaptcha_private_key` 字段中。

**如果是国内服务器**
请修改 `recaptcha_siteverify_api` 字段为 `https://recaptcha.net/recaptcha/api/siteverify`

**如果针对不能正常上网的客户**
修改以下文件:
`vi /home/synapse/lib/python2.7/site-packages/syweb/webclient/app/login/register.html`
`vi /home/synapse/lib/python2.7/site-packages/synapse/rest/client/v2_alpha/auth.py`

寻找以下内容:
`<script src="https://www.google.com/recaptcha/api.js?render=explicit" async defer></script>`
修改为:
`<script src="https://recaptcha.net/recaptcha/api.js?render=explicit" async defer></script>`

重新启动服务器即可。