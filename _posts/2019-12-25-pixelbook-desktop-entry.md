---
layout: post
title: Pixelbook 主菜单添加 Linux 应用启动图标
categories: ChromeOS
description: 手动添加 Linux 应用启动图标
keywords: Pixelbook, Linux, ChromeOS
---

Crostini 里装的多数应用都会自动创建主菜单图标，对于少数没有自动创建的或是想修改图标之类的可以自己手动创建。

ChromeOS 主菜单图标路径在 `/usr/share/applications`，命名规则是 `应用名.desktop`。

应用名必须为英文，其中首字母应该要大写（暂未确定是否一定要大写）。  
实际上该文件名可以是任意字符串，不一定需要是应用名，但是为了便于管理建议以应用名命名。

下面是一个我自己的 `android-studio.desktop` 文件内容示例：
```
[Desktop Entry]
Name=Android Studio
Exec=/opt/android-studio/bin/studio.sh
Icon=/opt/android-studio/bin/studio.png
Type=Application
Terminal = false
StartupWMClass=jetbrains-studio
```
其中  
`Name` 字段为在Launcher中显示的应用名  
`Comment` 在传统Linux桌面环境中似乎应该是鼠标长时间停留后给出的提示，但 ChromeOS 并无此功能，故似乎无用  
`Exec` 是你的应用程序的可执行文件的路径
`Icon` 是你的应用程序图标的路径
`Type` 和 `Terminal` 两个字段通常情况下照搬即可，如果有特殊要求请自行上网查找 Linux桌面图标相关内容。

另外需要注意，系统似乎并不会检测.desktop文件内容的变更。  
想要更新 Launcher 中图标的话可以这样解决  
>将你的 `.desktop` 文件更名，例如将 `Studio.desktop` 文件更名为 `Studio2.desktop` 文件，以此方式来使系统更新图标。当然图标变更后你也可以将文件名改回去。
