---
title: Web Extension 之初窥
date: 2024-10-21 17:26:21
tags:
   - Web Extension
   - Develop
---

![cover](cover.webp)

原来开发 Chrome Web Extension 也没有想象中那么难。
<!--more-->

## 开发

### 关键概念

与 Web 页面非常相似，根据 Chrome 提供的额外 API 可以控制浏览器的一些功能。

需要理解的关键概念有3个：

- **Popup UI:** 当点击扩展按钮时展示的界面
- **Content Script：**注入网页，可以和 DOM 元素交互
- **Background Script：**在后台运行，处理事件和数据

### Manifest

这是扩展项目的配置文件，类似于 `package.json`。浏览器会读取该配置，处理开发者信息、权限管理、版本号等。

### 打包

最终要打包成一个 Web 扩展需要特别的工具支持。例如 **[CRXJS](https://crxjs.dev/)** 是一个 Vite 的库，支持 React/Vue/Solid/Vanilla JavaScript 多种框架。你也可以自由选择其他适合的。

## 上架

上架Google Chrome Web Store 需要注册开发者账号。

注册账号需要支付5美元认证费用。

这里有需要注意的点：

1. 国内银联 VISA 双币卡不支持
2. 国外卡，选择账户注册地址时不能选择中国大陆，香港和台湾地区是可以的

## 常见问题

1. 上架前处理好数据的问题，准备好隐私协议/用户协议。
2. 使用 React/Vue 等主流框架都可以开发，使用 TypeScript 也没问题。
3. 注入网页的元素，特别注意好隔离。使用 ShadownDOM 技术防止和原网页的样式相互影响。
