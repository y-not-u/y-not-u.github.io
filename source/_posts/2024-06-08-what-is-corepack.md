---
title: Corepack 怎么使用？
date: 2024-06-08 17:49:58
tags:
  - nodejs
---

## 什么是 Corepack

![corepack](https://img.vatery.com/file/7e5b3a5699af7a718ac0a.png)
Corepack 是一个将 Node.js 与“包管理器”绑定在一起的工具，能够更容易的指定使用的“包管理器”版本。 (*Corepack 已经从 Node.js 14.19 版本开始默认携带发布*)

那么我们需要使用 Corepack 吗？

<!-- more -->

## 如何使用

1. 首先需要安装

如果你使用 Node.js 14.19 版本之后，那么已经不需要额外安装。

下一步则是启用 Corepack：

```bash
corepack enable
```

2. 配置你的项目

非常简单，只需要在项目的 `package.json` 中配置 `packageManager` 字段即可：

```json
{
  // npm
  "packageManager": "npm@10.8.1",
  // pnpm
  "packageManager": "pnpm@9.1.4",
  // yarn
  "packageManager": "yarn@3.1.1"
}
```

⚠️ 提示：你必须指定明确的包管理器版本号，而不能指定一个范围。

```json
{
  // 不能使用一个范围
  "packageManager": "npm@^10.8.1",

  // 不能指定为 "latest"
  "packageManager": "pnpm@latest",

  // 不能不提供版本号
  "packageManager": "yarn"
}
```

## 尝试一下吧

如果你在 `package.json` 内将 `packageManager` 配置为 **pnpm** ，执行 `pnpm install` 会提示：

```bash
Corepack is about to download https://registry.npmjs.org/pnpm/-/pnpm-9.1.4.tgz.

Do you want to continue? [Y/n]
```

选择 `Y` 就会下载并使用指定版本的 pnpm。

## 我们需要吗？

我觉得在自动化部署（CI/CD）中非常值得尝试，可以便利快捷地使用指定的包管理器。因为当下包管理器也是百花齐放的状态， `npm` , `yarn` , `pnpm` , `bun` 都比较完善且各有特点，更多是由于开发者的个人喜好而选择，但是在自动化部署中还需要额外执行安装指令，略显繁琐。Corepack 能解决这一小小的痛点可以尝试下。
