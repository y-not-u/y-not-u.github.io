---
title: 简单理解Nvim的cmp与lsp
date: 2023-09-02 23:51:10
tags:
 - nvim
---

![](https://vip2.loli.io/2023/09/02/6KaLAEdq4ezHr85.png)

有3个关键要素：

- LSP 管理器: `nvim-lspconfig`
- cmp 代码补全引擎插件:  `nvim-cmp`
- language server 管理器:  `mason.nvim`

<!--more-->

## LSP是什么

Language Server Protocol，实际意义为客户端与服务端通信协议。感谢微软的创造。

而language server是专门为单独语言启动的解析、处理服务，具体能够实现例如：

- 跳转定义
- 找到引用
- 完成提示
- 重命名
- 格式化
- 重构

等等操作，非常贴近于IDE的功能。

Nvim内置了[Nvim LSP Client](https://neovim.io/doc/user/lsp.html)的API接口，但是服务端的实际分析处理能力需要第三方提供。

原本的配置比较复杂繁琐，官方又制作 `nvim-lspconfig` 来管理各类第三方language server的配置。

## CMP是什么

全称是 `autocompeletion`翻译为自动补全比较合适。

最核心的功能是提供代码片段。

```lua
{
    'hrsh7th/nvim-cmp',
    event = "VeryLazy",
    config = function()
      local cmp = require("cmp")
      cmp.setup({
        snippet = { expand = function() end },
        sources = cmp.config.sources({
          { name = "nvim_lsp" },
          { name = "nvim_lua" },
          { name = "buffer" },
          { name = "path" },
        }),
      })
    end,
    dependencies = {
      "hrsh7th/cmp-nvim-lsp",
      "hrsh7th/cmp-nvim-lua",
      "hrsh7th/cmp-buffer",
      "hrsh7th/cmp-path",
    }
  }
```

其中sources则是代码片段的插件。

## Mason.nvim怎么用

如果你是用的了 `nvim-lspconfig` 管理lsp，那么配合mason需要一个中间桥梁 `mason-lspconfig`。

使用 `:Mason` 就能选择你所需要的 language server 进行安装。

```lua
{
	"neovim/nvim-lspconfig",
	config = function()
	  local lspconfig = require('lspconfig')
	  lspconfig.lua_ls.setup {}
	  lspconfig.tsserver.setup {}
	  lspconfig.tailwindcss.setup {}
	  lspconfig.marksman.setup {}
	  lspconfig.eslint.setup {}
	  lspconfig.html.setup {}
	  lspconfig.cssls.setup {}
	end,
	dependencies = {
	  "williamboman/mason-lspconfig.nvim",
	},
},
{
	"williamboman/mason.nvim",
	config = true
},
{
	"williamboman/mason-lspconfig.nvim",
	opts = {
	  ensure_installed = {
	    "lua_ls",
	    "tsserver",
	    "cssls",
	    "tailwindcss",
	    "marksman",
	    "html",
	    "eslint"
	  }
	},
	dependencies = {
	  "williamboman/mason.nvim"
	}
}
```
