# 内置工具

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/5-advanced/tools-notes.mini.jpeg" 
     alt="5.17 内置工具学霸笔记" 
     data-zoom-src="/images/5-advanced/tools-notes.jpeg" />

工具允许 LLM 在你的代码库中执行操作。OpenCode 内置了一组工具，你也可以通过 [自定义工具](13-custom-tools.md) 或 [MCP 服务器](07a-mcp-basics.md) 扩展。

默认情况下，所有工具都**启用**且无需权限即可运行。但你可以通过配置控制 [权限](05-permissions.md)。

## 配置

你可以全局或按代理配置工具。代理配置会覆盖全局设置。

默认所有工具设为 `true`。要禁用工具，设为 `false`。

### 全局配置

使用 `tools` 选项全局禁用或启用工具：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "write": false,
    "bash": false,
    "webfetch": true
  }
}
```

可以使用通配符一次控制多个工具。例如禁用某个 MCP 服务器的所有工具：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "mymcp_*": false
  }
}
```

### 按代理配置

在代理定义中使用 `tools` 覆盖全局设置：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "tools": {
    "write": true,
    "bash": true
  },
  "agent": {
    "plan": {
      "tools": {
        "write": false,
        "bash": false
      }
    }
  }
}
```

也可以在 Markdown 代理文件中配置：

```markdown
---
description: Read-only analysis agent
mode: subagent
tools:
  write: false
  edit: false
  bash: false
---

Analyze code without making any modifications.
```

## 内置工具列表

<AdInArticle />

### bash

在项目环境中执行 shell 命令。

```json
{
  "tools": {
    "bash": true
  }
}
```

允许 LLM 运行 `npm install`、`git status` 等终端命令。

### edit

使用精确字符串替换修改现有文件。

```json
{
  "tools": {
    "edit": true
  }
}
```

通过替换精确文本匹配来编辑文件。这是 LLM 修改代码的主要方式。

### write

创建新文件或覆盖现有文件。

```json
{
  "tools": {
    "write": true
  }
}
```

### read

从代码库读取文件内容。

```json
{
  "tools": {
    "read": true
  }
}
```

支持读取大文件的特定行范围。

### grep

使用正则表达式搜索文件内容。

```json
{
  "tools": {
    "grep": true
  }
}
```

跨代码库快速内容搜索。支持完整正则语法和文件模式过滤。

### glob

通过模式匹配查找文件。

```json
{
  "tools": {
    "glob": true
  }
}
```

使用 `**/*.js` 或 `src/**/*.ts` 等 glob 模式搜索文件。

### list

列出给定路径下的文件和目录。

```json
{
  "tools": {
    "list": true
  }
}
```

接受 glob 模式过滤结果。

### lsp（实验性）

与配置的 LSP 服务器交互，获取代码智能功能如定义、引用、悬停信息和调用层次。

> 仅当 `OPENCODE_EXPERIMENTAL_LSP_TOOL=true`（或 `OPENCODE_EXPERIMENTAL=true`）时可用。

```json
{
  "tools": {
    "lsp": true
  }
}
```

支持的操作：`goToDefinition`、`findReferences`、`hover`、`documentSymbol`、`workspaceSymbol`、`goToImplementation`、`prepareCallHierarchy`、`incomingCalls`、`outgoingCalls`。

### patch

对文件应用补丁。

```json
{
  "tools": {
    "patch": true
  }
}
```

### skill

加载 [技能](./03a-skills-basics)（`SKILL.md` 文件）并在对话中返回其内容。

```json
{
  "tools": {
    "skill": true
  }
}
```

可通过 `permission.skill` 控制加载技能的审批提示。

### todowrite

在编码会话期间管理待办列表。

```json
{
  "tools": {
    "todowrite": true
  }
}
```

创建和更新任务列表以跟踪复杂操作的进度。

> 子代理默认禁用此工具，但可手动启用。

### todoread

读取现有待办列表。

```json
{
  "tools": {
    "todoread": true
  }
}
```

> 子代理默认禁用此工具。

### webfetch

获取网页内容。

```json
{
  "tools": {
    "webfetch": true
  }
}
```

允许 LLM 获取和阅读网页，用于查阅文档或研究在线资源。

## 内部实现

`grep`、`glob` 和 `list` 等工具底层使用 [ripgrep](https://github.com/BurntSushi/ripgrep)。默认情况下 ripgrep 遵守 `.gitignore` 模式，因此 `.gitignore` 中列出的文件和目录会从搜索和列表中排除。

### 忽略模式

要包含通常会被忽略的文件，在项目根目录创建 `.ignore` 文件：

```text
!node_modules/
!dist/
!build/
```

这个 `.ignore` 文件允许 ripgrep 搜索 `node_modules/`、`dist/` 和 `build/` 目录，即使它们在 `.gitignore` 中。

## 相关资源

- [自定义工具](13-custom-tools.md) - 创建自定义工具
- [MCP 服务器](07a-mcp-basics.md) - 集成外部工具
- [权限配置](05-permissions.md) - 控制工具权限
