---
title: 我的 Arch Linux 基本配置
date: 2024-09-24 22:03:17
tags:
   - Linux
---

![](cover.webp)
所有用过 Arch Linux 的人，都会成为它的信徒。

<!--more-->

## **基本信息**

- 系统 OS：Arch Linux
- 桌面环境 Desktop Manager：Hyprland

## **软件**

- 终端 Terminal：Alacritty / Wezterm
- Shell: zsh
- 字体 Fonts: FireCode
- 输入法 Input Method：fcitx Rime
- 浏览器 Browser：Brave / Zen Browser
- 文件管理器 File Manager：Joshuto
- 文本编辑器 Text Editor：Neovim
- 启动器 Launcher：Rofi
- 状态栏 Status Bar：Waybar

## 桌面环境

### 平铺桌面 Hyprland

非常优雅的平铺桌面，丝滑的动画一下吸引了我，马上从 i3 转换过来。这里不得不吹捧下平铺桌面。我第一次在视频中看到这种交互方式，瞬间迷上了。能够最高效率的使用桌面空间，也能够切换不同工作空间(Workspace)。这一切都是通过键盘快捷键操作，没有多余的鼠标动作。

![2024-09-24_13-26-48.webp](arch-linux.webp)

### 启动器 Rofi

一个勉强够用的应用启动器，和 macOS 上的 Alfred / Raycast 相比差远了，离 Windows 上的 Power Toys 也是有一段距离。有些使用 Gnome 等桌面的也可以尝试下 Electron 开发的 uTools，这款是比较完善的国人开发的启动器。但是在 Hyprland 上有一些问题，所以我就没有使用。

Rofi 默认的主题比较粗糙，起初我也参考一些其他人的案例进行手动修改，但是最终在开源仓库里选择了一款，更省心和完善。https://github.com/newmanls/rofi-themes-collection

![image.webp](rofi.webp)

### 浏览器 Zen Browser

我在 Linux 桌面环境里使用过非常多种类的浏览器，不是因为我闲得蛋疼，只是因为 Wayland 的兼容问题。实际内心想哭。特别针对 Chromium 系列浏览器，fcitx 输入法的问题困扰已久。

原生的 Chrome 和 Chromium 及衍生版需要添加下列参数才能有正确的缩放比例，但是输入法有时仍会出错，要么无法输入中文，要么候选框偏移超级离谱。

```bash
--enable-features=UseOzonePlatform --ozone-platform=wayland --enable-wayland-ime
```

但是 Firefox 系列浏览器没有这个问题，对 Linux 环境非常友好，缺点是运行速度偏慢。Firefox 衍生的浏览器有很多，基本主打的都是开源、隐私，例如：Firefox / Waterfox / LibreWolf / Floorp / Zen Browser…，我都尝试过，有的平平无奇，有的甚至BUG百出。最后选择了 Zen Browser，功能比较稳定，侧边栏和分栏展示也是我中意的点。

![image.webp](zen-browser.webp)

### 动态墙纸 mpvpaper

核心功能来自于 mpv，且支持 mpv 透传参数，例如全屏、随机播放、循环播放等。我从网络上下载了一些 city walk 的视频作为背景，配合透明的 Neovim 编程沉浸其中。

## **开发环境**

我是一个 Web 全栈开发者，主要以 JavaScript / TypeScript / NodeJS 为主，偶尔也会使用 Go / Python 写一些小脚本。

### **编辑器 Neovim**

这是一个比较小众的选择，我从 Sublime Text → Atom → VSCode → Noevim 一路走来，只能说你想好好编写代码，千万不要碰 Vim / Emacs 之类的东西。搭配 Lazy.nvim 管理插件，打造到我现在用的顺手模样，没法说具体花多久时间，只能说一直在优化修改配置和挑选新的插件，不停地折腾。

现在也开始使用 **cursor** AI 编辑器，购买了 Pro 版本。AI 编程助手真的太香了。

我个人的详细 neovim dotfile 可以移步此处：https://github.com/y-not-u/dotfiles/tree/main/.config/nvim

### **NodeJS 版本管理器 Volta**

这是一个 Rust 语言编写的新兴 JavaScript 工具库管理工具，我已经用它替代掉了 NVM。

### **JS 运行时 Bun**

这是一个 Zig 语言编写的新兴 JavaScript 运行时，现在已经发布 1.0 正式版本，功能也日趋稳定。我在开发环境经常使用它。

### JavaScript 包管理器 Pnpm

从原先 npm 和 yarn 两分天下的局面下，pnpm 横空出世并牢牢占据一席之位。当下许多知名项目也优先选择使用 pnpm。目前 pnpm 最新版本是 9.11.0，更新非常频繁。

### Session 管理 Zellij

tmux 无疑是优秀且悠久的终端 sesssion 管理器，但是其操作非常考验使用者的熟练度。

而新秀 Zellij 则是更多考虑了交互，提供指令提示。对新手非常友好。

当然同时提供了相当强悍的自定义性，包括键位、布局等等。可谓老少咸宜。

![image.webp](zellij.webp)

### Git 版本控制 Lazygit

第一次用过 Lazygit 后再也不乐意徒手敲命令了🤣。

![image.webp](lazygit.webp)



最后，几年用 Linux 作为工作的操作系统，聊一点感受。
Linux 的最大优点便是，极大的自由度。可以让使用者自己选择喜爱的各类工具，甚至动手能力强的可以制造出最符合个人习惯的工具。最大的缺点则是，有时缺乏一些优质交互的软件，还有软件的不稳定性（内核还是非常稳定的，桌面就说不定了🐶）。丰富的开源项目、活跃的社区是其特色。衷心希望 Linux 生态生生不息。
