---
title: Keychron 系列在 Linux 上连接 VIA 网站设置改键

date: 2024-08-09 22:38:31
tags:
- Linux
- Keychron
- Keyboard
---

keychron 支持 [https://usevia.app](https://usevia.app) 和官网 [https://launcher.keychron.com](https://launcher.keychron.com) 进行配置。两者稍有不同。
下面特别讲讲在 Linux 的环境下如何进行配置。
我的系统环境是 Arch Linux，键盘是 Keychron Q60 Max。
![keychron-Q60-Max](https://cdn.sa.net/2024/08/09/i1eHWhnoqcmAJF2.png)
<!--more-->

> keychron 说明要进行改键需要使用有线连接，并使用 Chrome 内核的浏览器访问上面的网站。

## 连接 Chrome
一般在 macOS 和 Windows 主流系统上会比较顺畅。但在 Linux 上第一次使用的时候，非常可能遇到 `HID device connected` 的报错 ，看着意思是成功，实际却没有连接成功的情况。

在 Chrome 上可以通过访问 [chrome://device-log/](chrome://device-log/)  查看日志。
发现显示：

```plaintext
[17:41:40] Failed to open '/dev/hidraw1': FILE_ERROR_ACCESS_DENIED

[17:41:40] Access denied opening device read-write, trying read-only.
```

此时需要给予必要的权限：

```bash
sudo chmod a+rw /dev/hidraw1
```

刷新网页即可连接成功。

## 网站 VIA 配置

keychron 官网连接后可以直接操作改键，但是 VIA 官网则需要导入 JSON 配置文件才能识别。下面简单讲下 `usevia.app` 上如何导入 JSON 键位配置文件。

1. 进入 keychron 的配置文件和固件网站 [https://www.keychron.com/pages/firmware-and-json-files-of-the-keychron-qmk-keyboards](https://www.keychron.com/pages/firmware-and-json-files-of-the-keychron-qmk-keyboards)  找到相应型号进行下载

2. 在 usevia.app 上“设置”页面内打开 "Show Design Tab" 开关

3. 切换到出现的 "Design" 页面，点击上传刚刚下载的 JSON 文件

4. 然后在首页连接后展示的是相应型号的布局和额外的自定义键值
