---
title: 5.8b ACP 协议
subtitle: Zed、JetBrains、Neovim 等编辑器集成
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.8b"
duration: 15 分钟
practice: 20 分钟
level: 进阶
description: 通过 ACP 协议在 Zed、JetBrains、Neovim 等编辑器中使用 OpenCode。
tags:
  - ACP
  - Zed
  - JetBrains
  - Neovim
prerequisite:
  - 5.8a VS Code 扩展
---

# 5.8b ACP 协议

> 通过 ACP 协议在 Zed、JetBrains、Neovim 等编辑器中使用 OpenCode。

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/5-advanced/08b-acp-notes.mini.jpeg" alt="ACP协议学霸笔记" data-zoom-src="/images/5-advanced/08b-acp-notes.jpeg" />

---

## 学完你能做什么

- 理解 ACP 协议是什么
- 在 Zed 中配置 OpenCode
- 在 JetBrains IDE 中配置 OpenCode
- 在 Neovim 中配置 OpenCode

---

## 什么是 ACP

**ACP**（Agent Client Protocol）是一个开放协议，标准化代码编辑器和 AI 编程代理之间的通信。

- 官网：[agentclientprotocol.com](https://agentclientprotocol.com)
- 支持的编辑器列表：[ACP 进度报告](https://zed.dev/blog/acp-progress-report#available-now)

### 工作原理

```
编辑器 ←→ JSON-RPC (stdio) ←→ opencode acp
```

编辑器启动 `opencode acp` 作为子进程，通过 stdin/stdout 使用 nd-JSON（newline-delimited JSON）格式进行 JSON-RPC 通信。

---

## 启动 ACP 服务

```bash
opencode acp
```

### 命令参数

| 参数 | 说明 | 示例 |
|------|------|------|
| `--cwd` | 工作目录 | `--cwd /path/to/project` |
| `--port` | 监听端口 | `--port 4096` |
| `--hostname` | 监听主机名 | `--hostname 0.0.0.0` |

> 来源：`cli.mdx:481-487`、`acp.ts:16-20`

---

## Zed 配置

添加到 [Zed](https://zed.dev) 配置文件 `~/.config/zed/settings.json`：

```json
{
  "agent_servers": {
    "OpenCode": {
      "command": "opencode",
      "args": ["acp"]
    }
  }
}
```

### 使用方式

<AdInArticle />

1. 打开命令面板
2. 执行 `agent: new thread`

### 绑定快捷键（可选）

编辑 `keymap.json`：

```json
[
  {
    "bindings": {
      "cmd-alt-o": [
        "agent::NewExternalAgentThread",
        {
          "agent": {
            "custom": {
              "name": "OpenCode",
              "command": {
                "command": "opencode",
                "args": ["acp"]
              }
            }
          }
        }
      ]
    }
  }
]
```

---

## JetBrains IDE 配置

支持所有 JetBrains IDE（IntelliJ IDEA、WebStorm、PyCharm 等）。

根据 [官方文档](https://www.jetbrains.com/help/ai-assistant/acp.html) 创建 `acp.json`：

```json
{
  "agent_servers": {
    "OpenCode": {
      "command": "/absolute/path/bin/opencode",
      "args": ["acp"]
    }
  }
}
```

> **注意**：JetBrains 需要使用 opencode 的**绝对路径**。

### 查找 opencode 路径

```bash
# macOS / Linux
which opencode

# Windows
where opencode
```

### 使用方式

在 AI Chat 代理选择器中选择 "OpenCode"。

---

## Neovim 配置

### Avante.nvim

添加到 [Avante.nvim](https://github.com/yetone/avante.nvim) 配置：

```lua
{
  acp_providers = {
    ["opencode"] = {
      command = "opencode",
      args = { "acp" }
    }
  }
}
```

如需传递环境变量：

```lua
{
  acp_providers = {
    ["opencode"] = {
      command = "opencode",
      args = { "acp" },
      env = {
        OPENCODE_API_KEY = os.getenv("OPENCODE_API_KEY")
      }
    }
  }
}
```

### CodeCompanion.nvim

添加到 [CodeCompanion.nvim](https://github.com/olimorris/codecompanion.nvim) 配置：

```lua
require("codecompanion").setup({
  strategies = {
    chat = {
      adapter = {
        name = "opencode",
        model = "claude-sonnet-4",
      },
    },
  },
})
```

如需传递环境变量，请参考 [CodeCompanion 文档](https://codecompanion.olimorris.dev/configuration/adapters#environment-variables-setting-an-api-key)。

---

## 支持的功能

通过 ACP 使用 OpenCode，功能与终端版完全相同：

| 功能 | 支持 |
|------|------|
| 内置工具（文件操作、终端命令等） | ✅ |
| 自定义工具和斜杠命令 | ✅ |
| MCP 服务器 | ✅ |
| `AGENTS.md` 项目规则 | ✅ |
| 自定义格式化器 | ✅ |
| Agent 和权限系统 | ✅ |

### 不支持的功能

以下 TUI 专用命令在 ACP 模式下不可用：

- `/undo` - 撤销消息
- `/redo` - 重做消息

> 来源：`acp.mdx:147-149`

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|-----|-----|
| JetBrains 找不到命令 | 使用相对路径 | 改用 opencode 绝对路径 |
| Zed 无响应 | opencode 未安装或不在 PATH | 确认 `which opencode` 返回正确路径 |
| Neovim 环境变量无效 | 未正确传递 env | 使用 `env = { ... }` 配置 |
| `/undo` 不工作 | ACP 不支持此命令 | 这是预期行为，使用编辑器自带的撤销功能 |

---

## 相关资源

- [5.8a VS Code 扩展](./08a-ide-vscode) - VS Code/Cursor 扩展安装
- [速查/CLI 参考](../appendix/cli) - 完整命令行选项
- [ACP 官网](https://agentclientprotocol.com) - 协议规范

---

## 本课小结

你学会了：

1. ACP 协议的基本概念
2. Zed 编辑器配置（settings.json + keymap）
3. JetBrains IDE 配置（需要绝对路径）
4. Neovim 配置（Avante.nvim、CodeCompanion.nvim）
5. ACP 模式的功能限制

---

## 下一课预告

> 下一课我们将学习远程模式，在服务器上运行 OpenCode 并通过 Web 界面访问。
