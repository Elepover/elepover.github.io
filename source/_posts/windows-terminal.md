---
title: Windows Terminal 折腾日记 - 如何让 Windows 控制台更好看
date: 2020-03-30 00:00:00
tags:
- Windows Terminal
- Windows 10
- customization
- cygwin
- terminal
- oh-my-zsh
thumbnail: /pic/windowsterminal.png
---

相信大家早就看够了 Windows 的命令提示符了吧（笑

<!-- more -->

2019 年，微软首次[开源][1]并在 [Microsoft Store (Windows 1903+)][2] 上上架了一个全新的 Windows 终端应用，称为 “Windows Terminal”。

终于，微软给 Windows 提供了一个“现代化”的命令行程序。Windows Terminal 的功能主要有：

- 多标签页
- 24 位颜色
- 支持鼠标输入
- ANSI 转义序列
- 微软讲述人支持
- 主题、背景图片、全屏模式
- UTF-8 及 UTF-16 字符集支持
- 以 HTML 或富文本格式复制到剪贴板
- 基于 DirectWrite 的文本渲染硬件加速
- 应用全新的 Fluent Design，基于 UWP 平台

而 Windows Terminal 仍然处于 [Beta 阶段][3]，还没有图形化的设置界面，所有设置都需要通过修改 JSON 配置文件实现，对部分用户显得不太友好。

配置完成后，Windows Terminal 就像这样:

![最终效果图](/pic/finaleffect.png)

下面我们来一步步地把 Windows Terminal 调教成我们喜欢的样子。

## 安装 Windows Terminal

要体验 Windows Terminal，首先需要您的系统版本为 Windows 10 **1903** 或更高。

在 [Microsoft Store][2] 下载 Windows Terminal

## 安装 cygwin

[在此][4]下载 cygwin 安装程序，一路下一步。

当选择镜像源的时候，国内用户可以选择自带的 163（网易）镜像源，或使用[清华大学开源软件镜像站][5]所提供的镜像：

`https://mirrors.tuna.tsinghua.edu.cn/cygwin/`

接下来，在左上角搜索 `zsh` 包，双击 `Skip`，选择为最新版本，继续。

> 建议安装 `curl`, `wget`, `git` 及 `nano` 包。
>
> 如您已经安装 Git for Windows 则不需要 `git` 包。
>
> 如果需要 C/C++ 开发环境，可以安装对应的包，在此不再赘述。

## 获取字体

在这里推荐 [JetBrains Mono][6]，您也可以选用任何支持 Powerline 字符的字体。

## 配置 Windows Terminal 样式

在新建标签页按钮右侧的菜单中，选择 `Settings`

Windows Terminal 会尝试打开默认的 JSON 文件编辑器，可在 `profiles.list` 下新增如下配置：

```json
{
    // 使用亚克力材质透明效果
    "useAcrylic": true,
    // 亚克力材质不透明度
    "acrylicOpacity": 0.7,
    // 背景图片位置
    // 建议存放于 AppData 文件夹，您也可以自行选择其它路径
    "backgroundImage": "%AppData%\\Windows Terminal\\bg.jpg",
    // 背景图片对齐方向
    // topLeft: 左上角
    // topRight: 右上角
    // bottomLeft: 左下角
    // bottomRight: 右下角
    "backgroundImageAlignment": "topLeft",
    // 背景图片不透明度
    "backgroundImageOpacity": 0.7,
    // 背景图片拉伸模式
    // none: 无
    // fill: 填充
    // uniform: 适应
    // uniformToFill: 适应并填满区域
    "backgroundImageStretchMode": "uniformToFill",
    // 是否在 shell 退出时关闭窗口
    "closeOnExit": true,
    // 颜色配置文件，可选:
    // Campbell
    // Solarized Light
    // Solarized Dark
    // One Half Light
    // One Half Dark
    "colorScheme": "One Half Dark",
    // 启动 shell 时的命令行
    "commandline": "zsh.exe --login -i",
    // 光标形状
    // vintage: ▃
    // bar: ┃
    // underscore: ▁
    // filledBox: █
    // emptyBox: ▯
    "cursorShape": "underscore",
    // 字体 FontFamily 名称
    "fontFace": "JetBrains Mono",
    // 字体大小，磅数
    "fontSize": 10,
    // GUID，请尝试随机生成
    "guid": "{5dfce6c2-f7a3-4ae2-bd4e-096f2fd979a5}",
    // 是否隐藏该配置文件
    "hidden": false,
    // 历史记录大小（行数）
    "historySize": 10000,
    // 在下拉菜单中的图标
    "icon": "%HOMEDRIVE%\\cygwin\\Cygwin-Terminal.ico",
    // 配置文件名称
    "name": "cygwin/zsh",
    // 起始目录
    // 建议设置为 cygwin 的 home 文件夹
    "startingDirectory": "%HOMEDRIVE%\\cygwin\\home\\%Username%"
}
```

别忘了在顶层设置 `"defaultProfile": "{5dfce6c2-f7a3-4ae2-bd4e-096f2fd979a5}"`（此处填入你想设置为默认配置文件的 GUID）

## 配置 oh my zsh

### 安装 oh my zsh

启动 Windows Terminal，如果配置正确，此时应该启动的是 zsh 的默认界面。

按 Q 无视初次启动提示，直接运行如下命令：

`sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

> 若您没有安装 `curl` 包，请尝试下载上方命令链接中的脚本文件并通过 `chmod +x install.sh && ./install.sh` 的命令手动执行。
>
> 如出现 “`GnuTLS: Error in the pull function.`” 报错，请检查互联网连接。

oh my zsh 应该能自动进行安装。

### 配置主题

运行 `nano .zshrc` 以修改 zsh 配置文件

> 如您未安装 `nano` 软件包，也可以自己选择喜欢的文本编辑器。

将默认的 `ZSH_THEME=robbyrussell` 修改为 `ZSH_THEME=agnoster`，并保存。

> 如果您是中文系统，建议将 `#export LANG=en_US.UTF-8` 一行取消注释（去掉前导的 `#` 符号），以免发生文本错误。

重新打开 Windows Terminal 以使改动生效。

> 如果您遇到命令执行后需要数秒才能出现输入区域的问题，请尝试以下两个指令以禁用 git 扩展:
>
> `git config --add oh-my-zsh.hide-status 1`
>
> `git config --add oh-my-zsh.hide-dirty 1`

## 替换 Win + X 菜单

> 教程目前提供的内容仅适用于已在 `设置 -> 个性化 -> 任务栏` 中将“**当我右键单击“开始”按钮或按下 Windows 键 + X 时，在菜单中将命令提示符替换为 Windows PowerShell**”选项关闭的情况。

在资源管理器中进入如下路径：

`%AppData%\..\Local\Microsoft\Windows\WinX\Group3`

按住 Shift，右键空白区域，选择 "在此处打开 PowerShell 窗口"。

粘贴以下命令：

```powershell
mv '.\01 - Command Prompt.lnk' '.\01 - Command Prompt.lnk.bak'
mv '.\02 - Command Prompt.lnk' '.\02 - Command Prompt.lnk.bak'
```

从“开始”菜单中将 Windows Terminal 拖到桌面，再将快捷方式拖进文件夹中。

重命名为 `02 - Command Prompt`（扩展名应当已经被资源管理器隐藏）

重复操作，此次重命名为 `01 - Command Prompt`

运行以下命令：

```powershell
$file = "01 - Command Prompt.lnk"
$raw = [System.IO.File]::ReadAllBytes($file)
$raw[0x15] = raw[0x15] -bor 0x20
[System.IO.File]::WriteAllBytes($file, $raw)
```

这条命令强行将快捷方式不可勾选的“以管理员身份运行”选项设置为“开”，以实现以管理员权限运行 Windows Terminal 的效果。

此时，Windows + X 菜单的“命令提示符”及“命令提示符（管理员）”应当均正确指向 Windows Terminal 了。

  [1]: https://github.com/microsoft/terminal
  [2]: https://www.microsoft.com/store/productId/9N0DX20HK701
  [3]: https://en.wikipedia.org/wiki/Software_release_life_cycle#Beta
  [4]: https://cygwin.com/setup-x86_64.exe
  [5]: https://mirrors.tuna.tsinghua.edu.cn
  [6]: https://www.jetbrains.com/lp/mono/
