---
title: 连接 GLM-4.7
subtitle: 中文强、Coding Plan 可选
course: OpenCode 中文实战课
stage: 第一阶段
lesson: "1.4c"
duration: 10 分钟
practice: 5 分钟
level: 新手
description: 获取智谱 API Key，并在 OpenCode 中选择 GLM 模型完成第一次对话。
tags:
  - 模型
  - 智谱
  - GLM
  - API Key
prerequisite:
  - 1.2 安装
---

# 连接 GLM-4.7

## 📝 课程笔记

本课核心知识点整理：

<img src="/images/1-start/zhipu-notes.mini.jpeg" 
     alt="连接 GLM-4.7 学霸笔记" 
     data-zoom-src="/images/1-start/zhipu-notes.jpeg" />

---

> 预计时间：5-10 分钟

智谱 AI 在中文理解、写作/总结方面体验很好，**属于国产大模型第一梯队**，国内用户访问速度快、连接稳定。

GLM Coding Plan 套餐 **20 元/月起步**，相比按量计费更省。

### GLM-4.7 能力亮点

- 在推理、代码和智能体能力等方面达到开源模型 SOTA 水平
- Pro 套餐可用专属 MCP（含图像视频理解、联网搜索、网页读取、开源仓库），助力完成更广泛的开发任务


如果你还没看过"API Key 是什么"，建议先回到 [1.4 总览](./04-connect) 把概念弄清楚。

---

## 学完你能做什么

- 获取智谱 AI API Key
- 在 OpenCode 里连接智谱提供商
- 选择 GLM-4.7 并完成第一次对话

---

## 🎒 开始前的准备

- [ ] 完成了 [1.2 安装](./02-install)，能运行 `opencode`
- [ ] 已能访问智谱控制台并拿到 API Key

---

## 跟我做

### 第 1 步：注册并获取 API Key

推荐通过以下链接注册/登录：

- **教程站专属链接（优惠 10%）**：[https://www.bigmodel.cn/glm-coding?ic=PNSI6JU6FP](https://www.bigmodel.cn/glm-coding?ic=PNSI6JU6FP)

> **提示**：
> - GLM Coding Plan 有 Lite/Pro/Max 三个套餐，根据你的使用需求选择，建议选 Pro（量大管饱）。
> - 拿到 Key 后先保存好（不要发群、不要提交到 GitHub）。

---

### 第 2 步：在 OpenCode 中连接智谱

启动 OpenCode：

```bash
opencode
```

输入：

```
/connect
```

在提供商列表里搜索并选择：

- `Zhipu AI Coding Plan`

然后粘贴你的 API Key。

---

### 第 3 步：选择 GLM 模型

输入：

```
/models
```

选择你想用的模型（例如 `glm-4.7`）。

---

### 第 4 步：发送第一句话（验证成功）

```
你好，请介绍一下你自己
```

能收到回复就说明连接成功。

---

## 检查点 ✅

- [ ] `/models` 里能看到并选择 GLM 模型（例如 `glm-4.7`）
- [ ] 发送消息能收到 AI 回复

---

## 踩坑提醒

| 现象 | 原因 | 解决 |
|-----|------|------|
| 没有 `智谱 AI Coding Plan` 选项 | OpenCode 版本太旧 | 升级 OpenCode：`opencode upgrade` |
| `API key invalid` | Key 格式错误 | 确认是完整的 Key（通常类似 `xxx.xxx`） |

---

## 下一步

- 回到 [1.4 总览](./04-connect) 选择下一条路线，或进入 [2.1 界面与基础操作](../2-daily/01-interface)
