---
title: Windows 上的平铺桌面管理器 GlazeWM
date: 2025-01-30 12:23:25
tags:
  - Windows
  - App
---

我之前的 Web 开发工作，是可以完全在 Linux 上完成的。使用的是 Arch Linux 发行版搭配 Hyprland 平铺桌面，拥有最完善的 Wiki 和最丰富的软件源，凭借 Linux 的开放性，能够做到极致的个性化。

但是由于目前需要开发鸿蒙 App，只能更换到 Windows 上使用 DevEco Studio IDE 进行开发。于我而言，最离不开的还是**平铺桌面**。其次是**命令终端**。

之前尝试过 komorebi，感觉不是很顺手，然后切换到 GlazeWM。感觉瞬间回来了！

![demo](GlazeWM-Demo.webp)

<!--more-->

仓库地址：<https://github.com/glzr-io/glazewm>
<div class="tip">由于 release 版本和仓库最新的 main 分支代码有所区别，所以功能以及键位有差别。（例如最新的代码支持设置透明度 `set-opacity`，截至 2025.01.20 最新 release <b>v3.7.0</b> 并无该功能）</div>

## 特点

1. 使用 yaml 配置文件
2. 支持多显示器
3. 针对 window 的自定义 rules
4. 一键快速安装
5. 能够和 Zebar 状态栏搭配运行

## 安装

推荐通过 winget 进行安装：

```bash
winget install GlazeWM
```

## 配置

官方提供了一份配置 [sample-config.yaml](https://github.com/glzr-io/glazewm/blob/main/resources/assets/sample-config.yaml)可供参考。

如果希望有一些额外的个性化设置，下面的内容或许对你有所帮助。

Windows 上安装后默认的配置目录是 `~/.glzr/glazewm/config.yaml`

### 查看 window 的信息

推荐使用 [Winlister](https://www.nirsoft.net/utils/winlister.html) 能够查看所有开启窗口的 Title 和 Class 等信息。用于 rule 的匹配。

### 设置命令

GlazeWM 缺少一个更友好的官方文档说明。有许多命令并没有写在文档里，在我翻看了源码之后在这里简单整理下。

**设置窗口浮动**

*该命令支持的参数较多，可以自由搭配。*

`set-floating --centered --shown-on-top --x-pos 100 --y-pos 100 --width 400 --height 400`  设置浮动并指定窗口的大小和位置

**设置窗口位置**

`position --x-pos 200 --y-pos 0` 移动窗口到 x:200 y:0

**设置窗口大小**

`size --width 800 --height 600`

**重置窗口大小**

`resize --width +2% --height -2%`

**设置窗口全屏**

`toggle-fullscreen` 开关全屏

`set-fullscreen --maximized --shown-on-top` 设置全屏

**移动工作空间**

`move-workspace --direction [left|right|up|down]`

**移动窗口到工作空间**

`move --workspace 1` 移动窗口到工作空间1

**改变焦点**

`focus --workspace 1` 聚焦到工作空间1

`focus --next-active-workspace` 聚焦到下一个激活的工作空间

`focus --prev-active-workspace` 聚焦到上一个激活的工作空间

`focus --prev-workspace` 聚焦到上一个工作空间

`focus --next-workspace` 聚焦到下一个工作空间

`focus --recent-workspace` 聚焦到最近的工作空间

`focus --direction [left|right|up|down]` 聚焦到上下左右的工作空间

`focus --name browser` 聚焦到指定名字的工作空间

`focus --monitor-index 1` 聚焦到指定显示器

**忽略窗口**

`ignore`

**关闭窗口**

`close`

**最小化窗口**

`toggle-minimized` 开关窗口最小化

`set-minimized` 设置最小化窗口

**标题栏**

`set-title-bar-visibility [shown|hidden]` 设置标题栏是否展示

**执行命令**

`shell-exec explorer.exe` 打开文件管理器

**平铺窗口**

`toggle-tiling` 切换窗口平铺表现

`toggle-tiling-direction` 切换窗口平铺方向

`set-tilling-direction [horizontal|vertical]` 设置平铺方向

**窗口管理器**

`wm-exit` 退出窗口管理器

`wm-cycle-focus` 切换聚焦模式 窗口 → 浮动  → 全屏

`wm-disable-binding-mode --name resize`  停用绑定模式，按下 enter/escape 回退到默认快捷键绑定

`wm-enable-binding-mode --name resize` 启用绑定模式

`wm-redraw` 重绘所有窗口

`wm-reload-config` 重新加载配置文件

`wm-toggle-pause` 开关停用窗口管理器及其所有快捷键

## 个人感受

使用功能上还是比较完善的，虽然自定义化比不上 Linux 生态，但这是 Windows 系统的限制，并不能要求仅仅通过桌面管理器来解决。整体体验也是非常类似于 Linux 上的一些知名软件如 i3 / hyprland，能达到将近80%的效果。

另外Rust 语言开发也保证了性能。

提供的接口基本够用。例如移动窗口，更改窗口大小，设置窗口浮动等等。

软件稳定性不错，比较少遇到 bug。

项目稳定性也不错，积极更新。

如果你在 Windows 下想要尝试平铺桌面，推荐你试一试 GlazeWM。
