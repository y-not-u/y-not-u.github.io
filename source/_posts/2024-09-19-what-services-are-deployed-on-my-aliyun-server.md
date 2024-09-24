---
title: 我的阿里云服务器部署些什么
date: 2024-09-19 21:19:57
tags:
- linux
---

作为一个开发者，势必需要一台个人、且能公网访问的服务器。那么它能用来干什么呢？让我来带你看看我是如何玩转它的。
![cloud server](https://cdn.sa.net/2024/09/19/zWkBTKbGoJgPurt.png)
<!--more-->

## 操作系统

个人工作电脑使用的是 Arch Linux，但对于纯服务器来说，没必要追求非常新的特性，而是要求相对的稳定。又因为是个人服务器，有时会装一些较新的应用或者体验一些的功能，所以综合考虑使用 Ubuntu 22.04 LTS。Ubuntu 具备巨大的用户群体和丰富的网络资料，是非常适合个人学习或日常使用的。

## 必要准备

1. 首先创建一个普通用户，拒绝使用默认的 `root` 用户，避免权限、安全问题带来麻烦。
2. 本地机器创建密钥对，使用 ssh 钥匙串加密登录，而不是直接使用密码。
3. 服务器上操作首先开启 `tmux` ，防止操作窗口卡住丢失。
4. 之后就是愉快地玩耍 😉 。

## 基础软件

首先我会装一些必须的基础应用，帮助我更好管理、监控服务器。

### Docker

![image.png](https://cdn.sa.net/2024/09/19/PF8gOuozksxavMc.png)

这是必须的，现在非常多的应用都能支持容器化。带来快捷的部署体验。包括不需要再安装一系列的依赖污染服务器的环境。我也经常使用 **docker compose** 来部署服务，可以很好的管理配置参数。

### btop

![image.png](https://cdn.sa.net/2024/09/19/CGBsSQyRMun3lg8.png)

`htop` 的替代品。在这两个基本的设备检测软件之间选择一个即可。

### ncdu

![image.png](https://cdn.sa.net/2024/09/19/1OqlLMBxQNRb3Tp.png)

`du` 的替代品。查看磁盘使用状态非常方便。

## 常规应用

### Zerotier-one

![image.png](https://cdn.sa.net/2024/09/19/9wlcFM8qhtNpv1B.png)

组建私有网络，让多台电脑及服务器可以异地组网。这样不需要通过暴露外网端口即可进行互联互通，大大增加安全性，以及减少网络嵌套层级带来的麻烦。缺点是，由于需要打洞等技术，在可以直连情况下通信良好，在不能直连情况下走 Zerotier 的服务器会有巨大的延迟表现。目前通过搭建一个 moon 节点暂时缓解。

### Cloudflare Tunnel

![image.png](https://cdn.sa.net/2024/09/19/MsfmnZpXUx6wh4C.png)

使用 `Zero Trust` 零信任技术，能够将机器内部的服务端口暴露出来，这样不需要配置 nginx 反代，或者备案（特殊需要）就可以通过域名访问。唯一代价，速度有点慢。其余都是优点：免费、匿名、CDN。

### Uptime Kuma

![image.png](https://cdn.sa.net/2024/09/19/vYBFUZAsc1HCkxI.png)

网络监控、报警平台。界面舒适，功能适中，交互良好。这是一款个人使用非常方便的监控软件，可以通过 docker 部署，http/tcp/ping 等各类网络测试连通性，还支持数十种通知系统的接入。像我就接入了 iOS Bark App 来收取通知。

### Homarr

![image.png](https://cdn.sa.net/2024/09/19/Ss6MZB9XYhjOxqF.png)

用来导航的一个小网页。将自己部署的服务添加上去，作为一个面板墙或者设置为浏览器的默认导航页面，方便访问。同样支持 docker 部署。

### AList

![image.png](https://cdn.sa.net/2024/09/19/U9y61eBPHhV7aQl.png)

能够接入数十种网盘，并提供 webdav 服务的工具。由 Go 语言开发，性能不错。作者更新频繁，生态已经比较丰富，聚集了众多用户。我挂载了网盘之后，通过 `duplicati-cli` 备份加密数据到 webdav 后，就相当于有了一个数据备份网盘。另外也可以保存一些影视资料，使用第三方的播放器，如 Infuse/VidHub/Fileball 等挂载展示海报墙和播放，能够获得比较好的体验。

其余的工作开发所需例如数据库等等就不在此列举了，各人尽不相同。
