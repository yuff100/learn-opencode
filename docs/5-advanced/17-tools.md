# 内置工具

## 课程笔记

工具允许 LLM 在你的代码库中执行操作。OpenCode 内置了一组工具，你也可以通过 [自定义工具](13-custom-tools.md) 或 [MCP 服务器](07a-mcp-basics.md) 扩展。

默认情况下，所有工具都启用且无需权限即可运行。但你可以通过配置控制 [权限](05-permissions.md)。

## 配置

你可以全局或按代理配置工具。代理配置会覆盖全局设置。

默认所有工具设为 true。要禁用工具，设为 false。

### 全局配置

使用 tools 选项全局禁用或启用工具：

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

### 按代理配置

在代理定义中使用 tools 覆盖全局设置：

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

## 内置工具列表

### bash

在项目环境中执行 shell 命令。

```json
{
  "tools": {
    "bash": true
  }
}
```

允许 LLM 运行 npm install、git status 等终端命令。

### edit

使用精确字符串替换修改现有文件。

```json
{
  "tools": {
    "edit": true
  }
}
```

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

### grep

使用正则表达式搜索文件内容。

```json
{
  "tools": {
    "grep": true
  }
}
```

### glob

通过模式匹配查找文件。

```json
{
  "tools": {
    "glob": true
  }
}
```

### list

列出的文件和目录在给定路径下。

```json
{
  "tools": {
    "list": true
  }
}
```

### lsp（实验性）

与配置的 LSP 服务器交互，获取代码智能功能如定义、引用、悬停信息和调用层次。

### patch

对文件应用补丁。

```json
{
  "tools": {
    "patch": true
  }
}
```

### multiedit

批量修改同一个文件的多个位置。

```json
{
  "tools": {
    "multiedit": true
  }
}
```

### skill

加载 SKILL.md 文件并在对话中返回其内容。

```json
{
  "tools": {
    "skill": true
  }
}
```

### todowrite

在编码会话期间管理待办列表。

```json
{
  "tools": {
    "todowrite": true
  }
}
```

### todoread

读取现有待办列表。

```json
{
  "tools": {
    "todoread": true
  }
}
```

### webfetch

获取网页内容。

```json
{
  "tools": {
    "webfetch": true
  }
}
```

### websearch（实验性）

搜索网页内容，使用 OpenCode Exa AI 服务。

```json
{
  "tools": {
    "websearch": true
  }
}
```

> 需要设置环境变量 OPENCODE_ENABLE_EXA=true 或使用 OpenCode Zen 托管模型。

### codesearch（实验性）

搜索代码库和在线资源，查找相关 API、库和文档。

```json
{
  "tools": {
    "codesearch": true
  }
}
```

> 需要设置环境变量 OPENCODE_ENABLE_EXA=true 或使用 OpenCode Zen 托管模型。

### batch（实验性）

并行执行多个工具调用，优化性能。

```json
{
  "tools": {
    "batch": true
  }
}
```

> 最多允许 25 个工具调用。不允许的工具：batch、invalid、patch。

## 内部实现

grep、glob 和 list 等工具底层使用 ripgrep。默认情况下 ripgrep 遵守 .gitignore 模式，因此 .gitignore 中列出的文件和目录会从搜索和列表中排除。

### 忽略模式

要包含通常会被忽略的文件，在项目根目录创建 .ignore 文件：

```text
!node_modules/
!dist/
!build/
```

## 相关资源

- [自定义工具](13-custom-tools.md) - 创建自定义工具
- [MCP 服务器](07a-mcp-basics.md) - 集成外部工具
- [权限配置](05-permissions.md) - 控制工具权限
