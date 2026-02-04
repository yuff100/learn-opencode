---
title: opencode.json 配置详解
description: opencode.json 配置文件的详细参考手册，涵盖所有可用字段
---

# opencode.json 配置详解

> 本文档是 OpenCode 配置文件的完整参考手册，详细解释 `opencode.json` 中可用的每一个字段。

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/appendix/config-ref-notes.mini.jpeg"
     alt="配置选项参考学霸笔记"
     data-zoom-src="/images/appendix/config-ref-notes.jpeg" />

---

## 配置文件位置与优先级

OpenCode 按以下顺序加载配置（优先级从低到高，后者覆盖前者）：

1. **远程/默认配置**：从 `.well-known/opencode` 加载（如果配置了远程 Auth）
2. **全局配置**：`~/.config/opencode/opencode.json`
3. **自定义全局路径**：`OPENCODE_CONFIG` 环境变量指定的路径
4. **项目配置**：项目根目录下的 `opencode.json` 或 `opencode.jsonc`
5. **内联配置**：`OPENCODE_CONFIG_CONTENT` 环境变量的内容

---

## 顶层配置 (Top Level)

配置文件的根对象中包含的字段。

### 基础设置

| 字段 | 类型 | 说明 | 默认值 |
|------|------|------|--------|
| `username` | string | 在对话中显示的用户名。如果不设置，使用系统用户名。 | 系统用户 |
| `theme` | string | 界面主题名称。详见 [主题列表](../5-advanced/06a-themes)。 | - |
| `autoupdate` | boolean \| "notify" | 自动更新行为。`true`=自动更新，`false`=禁用，`"notify"`=仅通知。 | - |
| `logLevel` | enum | 日志级别。可选值：`"DEBUG"`, `"INFO"`, `"WARN"`, `"ERROR"`。 | `"INFO"` |
| `snapshot` | boolean | 是否启用 Git 快照备份机制。设为 `false` 禁用。 | `true` |

### 模型与 Agent

| 字段 | 类型 | 说明 |
|------|------|------|
| `model` | string | 主模型 ID (格式: `provider/model`)，用于复杂任务。 |
| `small_model` | string | 小模型 ID，用于生成标题、摘要等简单任务。 |
| `default_agent` | string | 默认启动的 Primary Agent 名称。默认为 `build`。 |

### 行为控制

| 字段 | 类型 | 说明 | 默认值 |
|------|------|------|--------|
| `share` | enum | 会话分享行为。`"manual"`(手动), `"auto"`(自动), `"disabled"`(禁用)。 | `"manual"` |
| `disabled_providers` | string[] | 禁用的 Provider 列表。即使有 Key 也不会加载。 | `[]` |
| `enabled_providers` | string[] | 仅启用的 Provider 列表。设置后，不在列表中的都会被忽略。 | - |

---

## 界面配置 (tui)

控制终端界面 (TUI) 的显示行为。

```json
"tui": {
  "scroll_speed": 3,
  "diff_style": "auto"
}
```

| 字段 | 类型 | 说明 | 默认值 |
|------|------|------|--------|
| `scroll_speed` | number | 鼠标滚轮滚动速度倍率（最小 0.001）。 | 3 |
| `scroll_acceleration` | object | 滚动加速配置。 | - |
| `scroll_acceleration.enabled` | boolean | 是否启用 macOS 风格的惯性滚动加速。 | `false` |
| `diff_style` | enum | 差异对比显示样式。`"auto"`(自适应), `"stacked"`(始终单列)。 | `"auto"` |

---

## Provider 配置 (provider)

配置模型提供商的 API Key、端点和模型参数。

**键名**：`provider` (单数)  
**类型**：`Record<string, ProviderConfig>`

```json
"provider": {
  "anthropic": {
    "options": {
      "apiKey": "sk-...",
      "timeout": 600000
    }
  }
}
```

### 通用选项 (options)

所有 Provider 都支持的 `options` 字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `apiKey` | string | API 密钥。建议使用 `{env:VAR}` 引用环境变量。 |
| `baseURL` | string | 自定义 API 端点地址（用于代理或兼容服务）。 |
| `timeout` | number \| false | 请求超时时间（毫秒）。默认 300000 (5分钟)。`false` 禁用超时。 |
| `setCacheKey` | boolean | 是否启用 Prompt Cache 键（用于 Anthropic/DeepSeek 等）。默认 `false`。 |
| `enterpriseUrl` | string | GitHub Enterprise URL (仅 Copilot Provider)。 |

### 模型特定配置 (models)

针对特定模型进行微调：

```json
"provider": {
  "anthropic": {
    "models": {
      "claude-3-7-sonnet": {
        "variants": {
          "thinking": { "disabled": true } // 禁用特定变体
        }
      }
    }
  }
}
```

### 黑白名单

在 Provider 配置对象内也可以控制模型列表：

| 字段 | 类型 | 说明 |
|------|------|------|
| `whitelist` | string[] | 仅允许使用的模型列表。 |
| `blacklist` | string[] | 禁止使用的模型列表。 |

---

## Agent 配置 (agent)

定义或覆盖 Agent 的行为。

**键名**：`agent` (单数)  
**类型**：`Record<string, AgentConfig>`

```json
"agent": {
  "code-reviewer": {
    "mode": "subagent",
    "prompt": "You are a code reviewer...",
    "permission": { "edit": "deny" }
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `description` | string | Agent 的简短描述，显示在 `/agent` 列表中。 |
| `mode` | enum | Agent 类型。`"primary"`(独立模式), `"subagent"`(子代理), `"all"`。 |
| `model` | string | 该 Agent 专用的模型 ID。 |
| `prompt` | string | System Prompt (人设指令)。 |
| `temperature` | number | 温度系数 (0.0 - 1.0)。 |
| `top_p` | number | 核采样参数 (0.0 - 1.0)。 |
| `steps` | number | 最大自动迭代步数。 |
| `color` | string | 在界面中显示的颜色 (Hex 格式，如 `#FF0000`)。 |
| `hidden` | boolean | 是否在 `@` 自动补全菜单中隐藏此 Agent。 |
| `permission` | object | 该 Agent 的专用权限配置 (覆盖全局权限)。 |
| `disable` | boolean | 是否禁用此 Agent。 |

---

## 权限配置 (permission)

控制 OpenCode 访问系统资源的权限。

**键名**：`permission` (单数)  
**类型**：`Record<string, Rule | Action>`

值可以是以下字符串之一（Action）：
- `"allow"`: 自动允许
- `"ask"`: 每次询问
- `"deny"`: 拒绝

也可以是对象（Rule）进行更细粒度控制。

```json
"permission": {
  "edit": "ask",
  "bash": {
    "*": "ask",
    "git *": "allow",
    "rm *": "deny"
  }
}
```

**可用权限项**：
- `read`: 读取文件
- `edit`: 编辑/写入文件
- `bash`: 执行命令
- `webfetch`: 访问网页
- `websearch`: 搜索引擎
- `codesearch`: 代码搜索
- `glob`: 文件查找
- `grep`: 内容搜索
- `list`: 列出目录
- `external_directory`: 访问外部目录
- `lsp`: LSP 操作
- `task`: 调用子 Agent

---

## 命令配置 (command)

定义自定义斜杠命令。

**键名**：`command` (单数)  
**类型**：`Record<string, CommandConfig>`

```json
"command": {
  "commit": {
    "template": "Generate a commit message for these changes:\n$DIFF",
    "agent": "build"
  }
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `template` | string | 提示词模板。支持 `$ARGUMENTS` 等变量。 |
| `description` | string | 命令描述。 |
| `agent` | string | 执行此命令的 Agent。 |
| `model` | string | 执行此命令的模型。 |
| `subtask` | boolean | 是否作为子任务运行。 |

---

## 快捷键配置 (keybinds)

自定义快捷键。

**键名**：`keybinds` (**复数**)

```json
"keybinds": {
  "leader": "ctrl+x",
  "session_new": "<leader>n"
}
```

常用配置项（完整列表见[快捷键速查](./keybinds.md)）：

- `leader`: 前缀键（默认 `ctrl+x`）
- `app_exit`: 退出应用
- `session_new`: 新建会话
- `session_list`: 会话列表
- `model_list`: 切换模型
- `agent_list`: 切换 Agent
- `input_submit`: 发送消息
- `input_newline`: 换行

---

## 服务器配置 (server)

配置 `opencode serve` 或 `opencode web` 的行为。

```json
"server": {
  "port": 4096,
  "hostname": "0.0.0.0",
  "mdns": true
}
```

| 字段 | 类型 | 说明 | 默认值 |
|------|------|------|--------|
| `port` | number | 监听端口。 | 4096 |
| `hostname` | string | 监听地址。启用 mdns 时默认为 `0.0.0.0`。 | 127.0.0.1 |
| `mdns` | boolean | 是否启用 mDNS 本地网络发现。 | false |
| `cors` | string[] | 允许跨域请求的来源列表。 | - |

---

## 实验性功能 (experimental)

启用正在开发中的实验性功能。**注意：这些功能不稳定，可能随时变更**。

```json
"experimental": {
  "batch_tool": true,
  "openTelemetry": true
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `batch_tool` | boolean | 启用批量操作工具。 |
| `openTelemetry` | boolean | 启用 OpenTelemetry 链路追踪。 |
| `chatMaxRetries` | number | 对话请求失败时的最大重试次数。 |
| `disable_paste_summary` | boolean | 禁用粘贴大段文本时的自动摘要。 |
| `continue_loop_on_deny` | boolean | 当工具调用被用户拒绝时，是否让 Agent 继续思考（而不是中断）。 |
| `primary_tools` | string[] | 指定仅限 Primary Agent 使用的工具列表。 |
| `mcp_timeout` | number | MCP 请求的全局超时时间（毫秒）。 |
| `hook` | object | 事件钩子配置。 |

### Hook 配置

```json
"experimental": {
  "hook": {
    "file_edited": {
      "*.ts": [{ "command": ["prettier", "--write", "$FILE"] }]
    },
    "session_completed": [{ "command": ["notify-send", "Done"] }]
  }
}
```

---

## 其他配置

### compaction (压缩)
控制上下文压缩行为。

```json
"compaction": {
  "auto": true,
  "prune": true
}
```
- `auto`: 上下文满时自动触发压缩。
- `prune`: 压缩时移除旧的工具输出。

### watcher (监视器)
控制文件系统监视。

```json
"watcher": {
  "ignore": ["node_modules/**", ".git/**"]
}
```
- `ignore`: 忽略监视的文件 glob 模式列表。

### instructions (指令)
```json
"instructions": ["docs/rules.md", ".cursor/rules/*.md"]
```
指定额外的全局指令文件列表。

### plugin (插件)
```json
"plugin": ["opencode-helicone-session", "./my-plugin.js"]
```
要加载的插件列表。支持 npm 包名或本地文件路径。

### mcp (扩展协议)
配置 Model Context Protocol 服务器。详见 [MCP 文档](../5-advanced/07a-mcp-basics)。

### formatter (格式化)
配置代码格式化工具。详见 [格式化器文档](../5-advanced/18-formatters)。

### lsp (语言服务)
配置 LSP 服务器。详见 [LSP 文档](../5-advanced/19-lsp)。

### enterprise (企业版)
```json
"enterprise": {
  "url": "https://github.example.com"
}
```
配置 GitHub Enterprise 实例地址。

---

## 附录：源码参考

<details>
<summary><strong>点击展开查看源码位置</strong></summary>

> 更新时间：2025-01-20

所有配置 Schema 定义均在 `packages/opencode/src/config/config.ts` 文件中。

| 配置项 | 对应 Schema | 行号范围 |
|--------|------------|----------|
| 顶层 Info | `Info` | L867-L1078 |
| Provider | `Provider` | L814-L865 |
| Agent | `Agent` | L550-L630 |
| Permission | `Permission` | L509-L539 |
| Keybinds | `Keybinds` | L632-L781 |
| TUI | `TUI` | L783-L795 |
| Server | `Server` | L797-L807 |
| Command | `Command` | L541-L548 |
| MCP | `Mcp` | L407-L472 |
| Experimental | `experimental` | L1029-L1071 |

</details>
