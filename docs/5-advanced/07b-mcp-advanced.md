---
title: 5.7b MCP 进阶
subtitle: OAuth、权限管理与常用服务
course: OpenCode 中文实战课
stage: 第五阶段
lesson: "5.7b"
duration: 20 分钟
practice: 20 分钟
level: 进阶
description: 学习 MCP OAuth 认证、权限管理、常用服务集成，构建安全的扩展体系。
tags:
  - MCP
  - OAuth
  - 权限管理
prerequisite:
  - 5.7a MCP 基础
  - 5.5 权限管控
---

# 5.7b MCP 进阶

> 💡 **一句话总结**：掌握 OAuth 认证、权限管理和常用 MCP 服务配置。

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/5-advanced/07b-mcp-advanced-notes.mini.jpeg" alt="MCP进阶学霸笔记" data-zoom-src="/images/5-advanced/07b-mcp-advanced-notes.jpeg" />

---

## 学完你能做什么

- 使用 OAuth 认证连接安全服务
- 管理 MCP 工具的权限和启用状态
- 在规则文件中集成 MCP 使用
- 配置常用 MCP 服务

---

## OAuth 认证

OpenCode 自动处理 OAuth 认证流程：

1. 检测到 401 响应，启动 OAuth 流程
2. 使用 **动态客户端注册 (RFC 7591)**（如服务器支持）
3. Token 安全存储在 `~/.local/share/opencode/mcp-auth.json`

### 自动认证

大多数情况下无需特殊配置：

```jsonc
{
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp"
    }
  }
}
```

首次使用时 OpenCode 会自动提示认证。

### 预注册客户端

如果服务器不支持动态注册，需要配置客户端凭证：

```jsonc
{
  "mcp": {
    "my-oauth-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": {
        "clientId": "{env:MY_MCP_CLIENT_ID}",
        "clientSecret": "{env:MY_MCP_CLIENT_SECRET}",
        "scope": "tools:read tools:execute"
      }
    }
  }
}
```

### 管理命令

```bash
# 手动触发认证
opencode mcp auth my-oauth-server

# 查看所有服务器认证状态
opencode mcp auth list

# 列出所有 MCP 服务器
opencode mcp list

# 移除存储的凭据
opencode mcp logout my-oauth-server

# 调试连接和 OAuth 流程
opencode mcp debug my-oauth-server
```

### 禁用 OAuth

如果服务器使用 API Key 而非 OAuth：

```jsonc
{
  "mcp": {
    "my-api-key-server": {
      "type": "remote",
      "url": "https://mcp.example.com/mcp",
      "oauth": false,
      "headers": {
        "Authorization": "Bearer {env:MY_API_KEY}"
      }
    }
  }
}
```

---

## 工具权限管理

<AdInArticle />

MCP 工具注册时使用 `{服务器名}_{工具名}` 格式命名。

### 全局禁用

使用 `permission` 配置禁用 MCP 工具：

```jsonc
{
  "mcp": {
    "my-mcp-foo": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-foo"]
    },
    "my-mcp-bar": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command-bar"]
    }
  },
  "permission": {
    "my-mcp-foo_*": "deny"
  }
}
```

使用通配符批量禁用：

```jsonc
{
  "permission": {
    "my-mcp*": "deny"
  }
}
```

### 按 Agent 启用

全局禁用后，在特定 Agent 中启用：

```jsonc
{
  "mcp": {
    "my-mcp": {
      "type": "local",
      "command": ["bun", "x", "my-mcp-command"]
    }
  },
  "permission": {
    "my-mcp*": "deny"
  },
  "agent": {
    "my-agent": {
      "permission": {
        "my-mcp*": "allow"
      }
    }
  }
}
```

### 通配符规则

- `*` 匹配零个或多个任意字符
- `?` 匹配正好一个字符
- 其他字符字面匹配

---

## 在规则文件中集成

在 `AGENTS.md` 或 `.opencode/agents/*.md` 中配置默认使用 MCP：

```markdown
## MCP 使用规则

当需要查询文档时，使用 `context7` 工具。

当不确定如何实现某功能时，使用 `gh_grep` 搜索 GitHub 代码示例。
```

这样 AI 会自动选择合适的 MCP 工具，无需每次在提示词中指定。

---

## 常用 MCP 推荐

### Sentry

连接 Sentry 监控平台，查询错误和问题：

```jsonc
{
  "mcp": {
    "sentry": {
      "type": "remote",
      "url": "https://mcp.sentry.dev/mcp",
      "oauth": {}
    }
  }
}
```

首次使用需要认证：

```bash
opencode mcp auth sentry
```

使用示例：

```
use sentry 查看最近未解决的错误
```

### Context7

搜索各种库和框架的文档：

```jsonc
{
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

使用 API Key 获取更高速率限制：

```jsonc
{
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    }
  }
}
```

使用示例：

```
use context7 查询 Cloudflare Worker 如何缓存 JSON 响应
```

### Grep by Vercel

搜索 GitHub 上的代码片段：

```jsonc
{
  "mcp": {
    "gh_grep": {
      "type": "remote",
      "url": "https://mcp.grep.app"
    }
  }
}
```

使用示例：

```
use the gh_grep tool 搜索 SST 框架中如何配置自定义域名
```

### Filesystem

本地文件系统操作（沙箱模式）：

```jsonc
{
  "mcp": {
    "filesystem": {
      "type": "local",
      "command": [
        "npx", "-y", "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ]
    }
  }
}
```

### Postgres

直接查询 PostgreSQL 数据库：

```jsonc
{
  "mcp": {
    "postgres": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-postgres"],
      "environment": {
        "POSTGRES_CONNECTION_STRING": "{env:DATABASE_URL}"
      }
    }
  }
}
```

### Puppeteer

浏览器自动化和网页抓取：

```jsonc
{
  "mcp": {
    "puppeteer": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

### Memory

持久化键值存储：

```jsonc
{
  "mcp": {
    "memory": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-memory"]
    }
  }
}
```

### SQLite

轻量级数据库操作：

```jsonc
{
  "mcp": {
    "sqlite": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-sqlite", "/path/to/database.db"]
    }
  }
}
```

### Slack

与 Slack 工作空间交互：

```jsonc
{
  "mcp": {
    "slack": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-slack"],
      "environment": {
        "SLACK_BOT_TOKEN": "{env:SLACK_BOT_TOKEN}",
        "SLACK_TEAM_ID": "{env:SLACK_TEAM_ID}"
      }
    }
  }
}
```

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|-----|-----|
| MCP 工具不出现 | 全局禁用或 Agent 未配置 | 检查 `permission` 配置 |
| OAuth 认证失败 | Token 过期或凭据无效 | 运行 `opencode mcp logout && opencode mcp auth` |
| 状态显示 `needs_client_registration` | 服务器不支持动态注册 | 在 `oauth` 中配置 `clientId` |
| 上下文快速耗尽 | 启用了太多 MCP 工具 | 禁用不常用的 MCP，使用按 Agent 启用 |
| 工具名称冲突 | 多个 MCP 有同名工具 | 使用 `{服务器名}_{工具名}` 格式区分 |
| 认证后仍显示 needs_auth | Token 存储失败 | 检查 `~/.local/share/opencode/mcp-auth.json` 权限 |

---

## 本课小结

你学会了：

1. **OAuth 认证**：自动处理或手动配置客户端凭证
2. **权限管理**：使用 `permission` 控制工具访问
3. **规则集成**：在 AGENTS.md 中配置默认 MCP 使用
4. **常用 MCP**：Sentry、Context7、Grep、Postgres 等

---

## 相关资源

- [5.7a MCP 基础](./07a-mcp-basics) - MCP 入门配置
- [5.1 配置全解](./01a-config-basics) - 配置文件基础
- [5.2 自定义 Agent](./02a-agent-quickstart) - Agent 工具配置
- [5.5 权限管控](./05-permissions) - 详细权限设置
- [官方 MCP 文档](https://opencode.ai/docs/mcp-servers/) - 英文原版

---

## 下一课预告

> 下一课我们将学习 IDE 集成，让 OpenCode 与 VS Code、JetBrains 等编辑器无缝协作。
