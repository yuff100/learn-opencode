---
title: 5.19 LSP 服务器
subtitle: 语言服务器协议集成
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.19"
duration: 10 分钟
level: 进阶
description: OpenCode 内置 LSP 服务器列表，支持 TypeScript、Python、Go 等语言，提供智能代码提示。
tags:
  - LSP
  - 语言服务器
  - 智能提示
prerequisite:
  - 5.1 配置全解
---

---
title: 5.19 LSP 服务器
subtitle: 语言服务器协议集成
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.19"
duration: 10 分钟
level: 进阶
description: OpenCode 内置 LSP 服务器列表，支持 TypeScript、Python、Go 等语言，提供智能代码提示。
tags:
  - LSP
  - 语言服务器
  - 智能提示
prerequisite:
  - 5.1 配置全解
---

# LSP 服务器

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/5-advanced/lsp-notes.mini.jpeg" 
     alt="5.19 LSP 服务器学霸笔记" 
     data-zoom-src="/images/5-advanced/lsp-notes.jpeg" />

OpenCode 与语言服务器协议（LSP）集成，帮助 LLM 与你的代码库交互。它使用诊断信息向 LLM 提供反馈。

## 内置 LSP 服务器

<AdInArticle />

OpenCode 内置了多种流行语言的 LSP 服务器：

| LSP 服务器 | 扩展名 | 要求 |
|-----------|--------|------|
| astro | .astro | Astro 项目自动安装 |
| bash | .sh, .bash, .zsh, .ksh | 自动安装 bash-language-server |
| clangd | .c, .cpp, .cc, .cxx, .c++, .h, .hpp, .hh, .hxx, .h++ | C/C++ 项目自动安装 |
| csharp | .cs | 已安装 .NET SDK |
| clojure-lsp | .clj, .cljs, .cljc, .edn | `clojure-lsp` 命令可用 |
| dart | .dart | `dart` 命令可用 |
| deno | .ts, .tsx, .js, .jsx, .mjs | `deno` 命令可用（自动检测 deno.json/deno.jsonc） |
| elixir-ls | .ex, .exs | `elixir` 命令可用 |
| eslint | .ts, .tsx, .js, .jsx, .mjs, .cjs, .mts, .cts, .vue | 项目中有 `eslint` 依赖 |
| fsharp | .fs, .fsi, .fsx, .fsscript | 已安装 .NET SDK |
| gleam | .gleam | `gleam` 命令可用 |
| gopls | .go | `go` 命令可用 |
| jdtls | .java | 已安装 Java SDK（21+） |
| kotlin-ls | .kt, .kts | Kotlin 项目自动安装 |
| lua-ls | .lua | Lua 项目自动安装 |
| nixd | .nix | `nixd` 命令可用 |
| ocaml-lsp | .ml, .mli | `ocamllsp` 命令可用 |
| oxlint | .ts, .tsx, .js, .jsx, .mjs, .cjs, .mts, .cts, .vue, .astro, .svelte | 项目中有 `oxlint` 依赖 |
| php intelephense | .php | PHP 项目自动安装 |
| prisma | .prisma | `prisma` 命令可用 |
| pyright | .py, .pyi | 已安装 `pyright` 依赖 |
| ruby-lsp (rubocop) | .rb, .rake, .gemspec, .ru | `ruby` 和 `gem` 命令可用 |
| rust | .rs | `rust-analyzer` 命令可用 |
| sourcekit-lsp | .swift, .objc, .objcpp | 已安装 swift（macOS 上为 xcode） |
| svelte | .svelte | Svelte 项目自动安装 |
| terraform | .tf, .tfvars | 从 GitHub releases 自动安装 |
| tinymist | .typ, .typc | 从 GitHub releases 自动安装 |
| typescript | .ts, .tsx, .js, .jsx, .mjs, .cjs, .mts, .cts | 项目中有 `typescript` 依赖 |
| vue | .vue | Vue 项目自动安装 |
| yaml-ls | .yaml, .yml | 自动安装 Red Hat yaml-language-server |
| zls | .zig, .zon | `zig` 命令可用 |

检测到上述文件扩展名且满足要求时，LSP 服务器自动启用。

> 设置环境变量 `OPENCODE_DISABLE_LSP_DOWNLOAD=true` 可禁用自动下载 LSP 服务器。

## 工作原理

当 OpenCode 打开文件时：

1. 根据文件扩展名检查所有已启用的 LSP 服务器
2. 如果尚未运行，启动相应的 LSP 服务器

## 配置

通过配置文件的 `lsp` 部分自定义 LSP 服务器：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {}
}
```

每个 LSP 服务器支持以下选项：

| 属性 | 类型 | 说明 |
|------|------|------|
| `disabled` | boolean | 设为 `true` 禁用该 LSP 服务器 |
| `command` | string[] | 启动 LSP 服务器的命令 |
| `extensions` | string[] | 该 LSP 服务器处理的文件扩展名 |
| `env` | object | 启动服务器时的环境变量 |
| `initialization` | object | 发送给 LSP 服务器的初始化选项 |

### 禁用 LSP 服务器

全局禁用**所有** LSP 服务器：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": false
}
```

禁用**特定** LSP 服务器：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "typescript": {
      "disabled": true
    }
  }
}
```

### 自定义 LSP 服务器

可以添加自定义 LSP 服务器：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": {
    "custom-lsp": {
      "command": ["custom-lsp-server", "--stdio"],
      "extensions": [".custom"]
    }
  }
}
```

## 附加信息

### PHP Intelephense

PHP Intelephense 通过许可证密钥提供高级功能。可以将许可证密钥放在文本文件中：

- macOS/Linux: `$HOME/intelephense/licence.txt`
- Windows: `%USERPROFILE%/intelephense/licence.txt`

文件应只包含许可证密钥，无其他内容。

## 相关资源

- [代码格式化器](18-formatters.md) - 自动代码格式化
- [内置工具](17-tools.md) - LSP 工具使用
