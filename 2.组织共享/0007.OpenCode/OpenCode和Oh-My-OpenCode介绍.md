# OpenCode & Oh My OpenCode 完全指南

> 本文档面向团队内部技术分享，系统介绍 OpenCode AI 编程助手及其核心插件 Oh My OpenCode 的功能特性、使用方法和最佳实践。

---

## 目录

1. [概述与定位](#1-概述与定位)
2. [OpenCode 核心概念](#2-opencode-核心概念)
3. [OpenCode 基础操作](#3-opencode-基础操作)
4. [OpenCode 进阶功能](#4-opencode-进阶功能)
5. [Oh My OpenCode 插件简介](#5-oh-my-opencode-插件简介)
6. [Sisyphus 多智能体编排系统](#6-sisyphus-多智能体编排系统)
7. [专业智能体详解](#7-专业智能体详解)
8. [分类系统与任务委托](#8-分类系统与任务委托)
9. [Hooks 钩子系统](#9-hooks-钩子系统)
10. [MCP 协议集成](#10-mcp-协议集成)
11. [命令系统](#11-命令系统)
12. [配置与定制](#12-配置与定制)
13. [实战工作流](#13-实战工作流)
14. [常见问题与技巧](#14-常见问题与技巧)

---

## 1. 概述与定位

### 1.1 什么是 OpenCode？

OpenCode 是一个**开源的终端 AI 编程助手**（MIT 许可证），GitHub 星标 70,000+，已被超过 650,000 名开发者使用。它用 Go 语言编写，提供：

- **终端原生界面**：基于 Bubble Tea 的 TUI 界面，支持 Vim 式导航
- **多模型灵活切换**：支持 OpenAI、Anthropic Claude、Google Gemini、AWS Bedrock、Groq、Azure OpenAI、OpenRouter 等
- **隐私优先**：代码不经过 OpenCode 服务器，支持本地模型离线运行
- **工具集成**：AI 可执行命令、搜索文件、修改代码

### 1.2 什么是 Oh My OpenCode？

Oh My OpenCode（简称 **OMO** 或 **oh-my-opencode**，也称 oh-my-openagent）是 OpenCode 的**高级插件**，将单一的 AI 助手升级为完整的**多智能体工程团队**。

核心升级包括：
- 🏗️ **Sisyphus 编排系统**：主智能体协调多个专业子智能体并行工作
- 🤖 **11 个专业智能体**：每个智能体有独立角色、优化的模型和明确的工具权限
- 🪝 **20+ 工作流自动化钩子**：自动压缩、验证、上下文注入
- 🔌 **内置 MCP**：Context7（文档）、Exa（搜索）、Grep.app（GitHub 搜索）
- 💻 **LSP + AST-Grep**：IDE 级重构能力

### 1.3 核心哲学

> **"Human intervention during agentic work is fundamentally a failure signal."**
> 
> 人类在智能体工作过程中的干预本质上是失败信号。系统应该自主完成任务，而不是半途而废。

Sisyphus 的名字来源于希腊神话——西西弗斯被惩罚永远推石头上山。这个系统的设计哲学是：**智能体必须完成所有 TODO（推完石头）才能停止**。这不是惩罚，而是质量保证机制。

---

## 2. OpenCode 核心概念

### 2.1 架构概览

```
┌─────────────────────────────────────────────────────┐
│                     OpenCode                         │
├─────────────────────────────────────────────────────┤
│  cmd/          CLI 入口 (Cobra)                     │
│  internal/app/ 核心应用服务                          │
│  internal/config/ 配置管理                          │
│  internal/db/  SQLite 数据库（会话存储）             │
│  internal/llm/ LLM 提供商集成                       │
│  internal/tui/ 终端 UI 组件                          │
│  internal/tools/ 工具集成                           │
└─────────────────────────────────────────────────────┘
           │
           │ + Oh My OpenCode 插件
           ▼
┌─────────────────────────────────────────────────────┐
│  Sisyphus Orchestrator    ← 主编排器                │
│  ├── Oracle              ← 问答                     │
│  ├── Explore             ← 代码探索                 │
│  ├── Librarian           ← 文档探索                 │
│  ├── Atlas               ← TODO 列表编排             │
│  ├── Hephaestus          ← 深度自主执行              │
│  └── Metis               ← 预规划顾问               │
└─────────────────────────────────────────────────────┘
```

### 2.2 关键术语

| 术语 | 定义 |
|------|------|
| **TUI** | Terminal User Interface，终端用户界面 |
| **Agent** | 智能体，能够执行特定任务的 AI 实例 |
| **Primary Agent** | 主智能体，处理主要对话的智能体 |
| **Subagent** | 子智能体，通过 `@agent_name` 调用的专业智能体 |
| **Tool** | 工具，智能体可以调用的操作（如读文件、执行命令） |
| **MCP** | Model Context Protocol，模型上下文协议 |
| **LSP** | Language Server Protocol，语言服务器协议 |
| **Hook** | 钩子，在特定事件自动触发的自动化脚本 |

### 2.3 智能体类型

OpenCode 内置两种主智能体：

| 智能体 | 模式 | 用途 |
|--------|------|------|
| **Build** | primary | 默认主智能体，启用所有工具，标准开发工作 |
| **Plan** | primary | 限制模式，用于规划和分析，可配置权限 |

Oh My OpenCode 扩展了这些，增加了 Sisyphus、Oracle、Explore 等专业编排智能体。

---

## 3. OpenCode 基础操作

### 3.1 安装

```bash
# 推荐：安装脚本
curl -fsSL https://opencode.ai/install | bash

# Node.js
npm install -g opencode-ai

# Homebrew (macOS/Linux)
brew install opencode-ai

# Docker
docker run -it opencode-ai/opencode
```

### 3.2 首次配置

```bash
# 1. 启动 OpenCode
cd your-project
opencode

# 2. 连接 LLM 提供商
/connect

# 3. 初始化项目（生成 AGENTS.md）
/init
```

### 3.3 基础命令

| 命令 | 说明 |
|------|------|
| `/init` | 分析项目结构，生成 `AGENTS.md` |
| `/connect` | 连接 LLM 提供商 |
| `/help` | 显示帮助 |
| `/agent on/off` | 启用/禁用 Agent 模式 |
| `/undo` | 撤销上一步操作 |

### 3.4 快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+C` | 退出 |
| `Ctrl+?` | 切换帮助对话框 |
| `Ctrl+L` | 查看日志 |
| `Ctrl+A` | 切换会话 |
| `Tab` | 切换主智能体 |

### 3.5 基础交互示例

```bash
# 询问代码问题
How is authentication handled in @packages/functions/src/api/index.ts

# 请求添加功能
Add a REST API endpoint for user registration

# 让 AI 先做计划
Plan the implementation of a caching layer
```

### 3.6 Agent 模式

Agent 模式允许 AI 自主执行多步骤任务，无需手动确认：

```bash
# 启动时启用
opencode --agent

# 或在会话内启用
> /agent on
Agent mode enabled. I can now execute multi-step tasks autonomously.
```

**Agent 模式特点**：
- 规划 → 执行 → 适应 → 报告的循环
- 支持暂停/恢复执行
- 最大步骤数可配置

---

## 4. OpenCode 进阶功能

### 4.1 自定义命令

创建自定义命令，触发预定义提示词模板：

```bash
# 项目级命令
.opencode/commands/*.md
~/.config/opencode/commands/*.md

# 用户级命令
~/.config/opencode/commands/*.md
```

**命令格式**：
```markdown
---
description: 创建 React 组件
---
Create a new React component at {{path}} with TypeScript and CSS modules.
```

**使用**：
```
/create-component path=src/components/Button.tsx
```

### 4.2 AGENTS.md 规则文件

在项目根目录创建 `AGENTS.md`，提供项目特定的上下文和规则：

```markdown
# 我的项目

## 技术栈
- TypeScript + strict mode
- React 18
- Tailwind CSS

## 代码规范
- 使用函数式组件
- 组件放在 `src/components/`
- 测试文件放在 `__tests__/`

## 项目结构
- `packages/` - 工作区包
- `apps/` - 应用入口
```

**优先级**：
1. 本地 `AGENTS.md`（项目根目录）
2. 全局 `~/.config/opencode/AGENTS.md`
3. `~/.claude/CLAUDE.md`（兼容 Claude Code）

### 4.3 非交互模式

```bash
# 单次提示并输出结果
opencode "Explain this function" src/utils/helper.ts

# 安静模式（不启动 TUI）
opencode -q "Fix the bug in auth.ts"
```

### 4.4 配置示例

```json
{
  "providers": {
    "anthropic": {
      "apiKey": "sk-..."
    },
    "openai": {
      "apiKey": "sk-..."
    }
  },
  "agent": {
    "build": {
      "model": "anthropic/claude-sonnet-4-20250514",
      "maxTokens": 5000
    }
  },
  "tools": {
    "bash": true,
    "edit": true,
    "grep": true
  }
}
```

---

## 5. Oh My OpenCode 插件简介

### 5.1 安装

```bash
# 通过 npm 安装
npm install -g oh-my-opencode

# 或通过 OpenCode 插件市场
/plugin marketplace add https://github.com/code-yeongyu/oh-my-opencode
/plugin install oh-my-opencode
```

### 5.2 核心特性

| 特性 | 说明 |
|------|------|
| **Sisyphus 编排器** | 主协调器，协调多个专业智能体 |
| **11 个专业智能体** | 各有专长，自动路由 |
| **20+ Hooks** | 工作流自动化 |
| **MCP 集成** | Context7、Exa、Grep.app |
| **Category 系统** | 任务自动分类和委托 |
| **Ultrawork 模式** | 最大并行化执行 |
| **Hashline** | 基于哈希的编辑工具，消除陈旧行错误 |

### 5.3 配置位置

```
项目级: .opencode/oh-my-opencode.json
用户级: ~/.config/opencode/oh-my-opencode.json
```

---

## 6. Sisyphus 多智能体编排系统

### 6.1 什么是 Sisyphus？

Sisyphus 是 Oh My OpenCode 的**主编排智能体**，其核心思想是：

> 不让一个"全才"做所有事，而是让专业的人做专业的事。

**传统方式的问题**：
- 单个 AI 处理所有任务，效率低
- 同一文件反复修改，需要重复读取上下文
- 任务只能排队等待

**Sisyphus 的解决方案**：
- 主智能体理解真实需求（不仅仅是表面请求）
- 将任务分解为专业子任务
- 并行委托给多个专家同时工作
- 必须完成所有 TODO 才能停止

### 6.2 工作原理

```
┌──────────────────────────────────────────────────────┐
│                     用户请求                         │
└─────────────────────┬────────────────────────────────┘
                      │
                      ▼
┌──────────────────────────────────────────────────────┐
│              ⚡ Sisyphus (主编排器)                   │
│  ┌─────────────────────────────────────────────────┐ │
│  │ 1. 理解真实需求                                   │ │
│  │ 2. 制定执行计划                                   │ │
│  │ 3. 并行委托给专业智能体                           │ │
│  │ 4. 协调和验证结果                                 │ │
│  │ 5. 确保所有 TODO 完成                             │ │
│  └─────────────────────────────────────────────────┘ │
└─────────────────────┬────────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   ┌─────────┐   ┌─────────┐   ┌─────────┐
   │ Explore │   │ Librarian│   │ Oracle  │
   │ 代码探索 │   │ 文档探索 │   │ 问答智能 │
   └─────────┘   └─────────┘   └─────────┘
        │             │             │
        └─────────────┴─────────────┘
                      │
                      ▼
┌──────────────────────────────────────────────────────┐
│              ✅ 验证与汇总                            │
└──────────────────────────────────────────────────────┘
```

### 6.3 何时使用 Sisyphus

| 场景 | 推荐程度 | 替代方案 |
|------|----------|----------|
| 复杂功能开发（3+ 步骤） | ✅ 强烈推荐 | Prometheus + Atlas |
| 已知 bug 的快速修复 | ✅ 适合 | 直接让 Sisyphus 处理 |
| 需要研究多个仓库/文档 | ✅ 强烈推荐 | Sisyphus 并行委托 |
| 快速单一文件修改 | ❌ 不推荐 | 使用 `/refactor` |
| 架构决策 | ❌ 不推荐 | 使用 Oracle |

### 6.4 核心配置

| 配置项 | 值 | 说明 |
|--------|-----|------|
| 推荐模型 | `anthropic/claude-opus-4-5` | Sisyphus 专用模型 |
| 思考预算 | 32k tokens | 仅 Anthropic 模型 |
| Temperature | 0.1 | 代码智能体固定低温度 |
| 最大 Tokens | 64000 | 单次响应上限 |

---

## 7. 专业智能体详解

### 7.1 智能体总览

| 智能体 | 模型 | 角色 |
|--------|------|------|
| **Sisyphus** | Claude Opus 4.5 | 主编排器，规划、委托、执行 |
| **Sisyphus-Junior** | 分类相关 | 任务委托执行器 |
| **Oracle** | 多种 | 问答和解释 |
| **Explore** | 多种 | 代码导航和发现 |
| **Librarian** | 多种 | 文档和代码探索 |
| **Atlas** | Claude Sonnet 4.6 | TODO 列表编排 |
| **Hephaestus** | GPT-5.3 Codex | 深度自主执行者 |
| **Metis** | Claude Opus 4.5 | 预规划顾问 |
| **Prometheus** | Claude Opus 4.5 | 规划器 |

### 7.2 核心智能体详解

#### Sisyphus
- **类型**：Orchestrator（编排器）
- **模型**：Claude Opus 4.5
- **特点**：长 mechanics-driven 提示词（~1100 行）
- **职责**：协调整个团队，确保所有 TODO 完成

#### Oracle
- **类型**：Communicator
- **职责**：问答、架构咨询
- **特点**：提供权威性回答，基于代码库上下文

#### Explore
- **类型**：Explorer
- **职责**：代码导航、发现
- **使用**：Find usages、grep 模式

#### Librarian
- **类型**：Explorer
- **职责**：文档和代码探索
- **使用**：理解代码库、查找相关代码

### 7.3 调用智能体

```bash
# 直接调用
@oracle 请审查这个设计并提出架构建议

# 在 Sisyphus 内部自动调用
# Sisyphus 会根据任务类型自动委托
```

### 7.4 创建自定义智能体

```bash
# 交互式创建
opencode agent create
```

或创建 Markdown 文件：

```markdown
---
description: 代码审查智能体
mode: subagent
permission:
  edit: deny
  bash:
    "*": ask
---

你是一个代码审查专家。专注于安全、性能和可维护性。
```

保存到 `.opencode/agents/review.md`，通过 `@review` 调用。

---

## 8. 分类系统与任务委托

### 8.1 什么是 Category？

Category 是一个**任务配置预设**，根据工作类型自动选择最佳模型：

| Category | 默认模型 | 用途 |
|----------|----------|------|
| `visual-engineering` | Gemini 3.1 Pro | 前端、UI/UX、设计、动画 |
| `ultrabrain` | GPT-5.4 | 深度逻辑推理、复杂架构 |
| `deep` | Claude Opus 4.5 | 深度研究和问题解决 |
| `artistry` | GPT-5.4 | 创意解决方案 |
| `quick` | Haiku | 简单快速任务 |
| `unspecified-low` | 默认 | 低优先级任务 |
| `unspecified-high` | 默认 | 高优先级任务 |
| `writing` | Claude | 文档写作 |

### 8.2 使用 Category

在任务委托时指定：

```typescript
task({
  category: "visual-engineering",
  prompt: "为仪表盘页面添加响应式图表组件",
});
```

### 8.3 自定义 Category

```json
{
  "categories": {
    "data-science": {
      "model": "anthropic/claude-sonnet-4.5",
      "temperature": 0.3
    }
  }
}
```

---

## 9. Hooks 钩子系统

### 9.1 什么是 Hook？

Hook 是在特定事件（如工具调用、消息发送）发生时自动触发的自动化脚本。

### 9.2 内置 Hooks

| Hook | 事件 | 说明 |
|------|------|------|
| `preemptive-compaction` | 上下文满前 | 预压缩上下文 |
| `ralph-loop` | 关键字 | Ralph 循环管理 |
| `thinking-block-validator` | 消息 | 验证思考块 |
| `compaction-context-injector` | 压缩时 | 管理上下文压缩 |
| `empty-message-sanitizer` | 消息 | 清理空消息 |
| `todo-continuation-enforcer` | 任务完成 | 强制 TODO 延续 |
| `comment-checker` | 编辑 | 检查注释 |
| `directory-readme-injector` | 文件读取 | 自动添加 README |

### 9.3 配置 Hooks

```json
{
  "disabled_hooks": ["comment-checker"],
  "experimental": {
    "aggressive_truncation": false,
    "auto_resume": true,
    "preemptive_compaction": true
  }
}
```

---

## 10. MCP 协议集成

### 10.1 内置 MCP

Oh My OpenCode 默认启用以下 MCP：

| MCP | 用途 | 启用状态 |
|-----|------|----------|
| **Context7** | 获取官方文档 | ✅ 默认 |
| **Exa** | 网络搜索 | ✅ 默认 |
| **Grep.app** | GitHub 搜索 | ✅ 默认 |
| **LSP** | 语言服务器 | ✅ 默认 |

### 10.2 使用 MCP

```bash
# 自动调用（无需配置）
# 当询问库的使用方法时，Context7 自动获取最新文档

# 手动搜索
使用 Exa 搜索 React Server Components 的最新实践
```

### 10.3 自定义 MCP

```json
{
  "mcpServers": {
    "my-mcp": {
      "command": "npx",
      "args": ["-y", "@my/mcp-server"]
    }
  }
}
```

---

## 11. 命令系统

### 11.1 内置命令

| 命令 | 说明 |
|------|------|
| `/init-deep` | 初始化分层 AGENTS.md 知识库 |
| `/ralph-loop` | 启动自我引用开发循环 |
| `/ulw-loop` | 启动 Ultrawork 循环（最大并行） |
| `/cancel-ralph` | 取消活动 Ralph 循环 |
| `/refactor` | 智能重构（LSP + AST-Grep + TDD 验证） |
| `/start-work` | 从 Prometheus 计划启动工作 |

### 11.2 Ultrawork 模式

Ultrawork 是**最大并行化**模式，一次性激活所有功能：

```bash
# 使用 ultrawork 关键词
ulw 为 Next.js 应用添加认证功能

# 等同于
ultrawork: 为 Next.js 应用添加认证功能
```

**特点**：
- 所有子任务并行执行
- 不需要 Team 界面
- 适合快速修复和重构

### 11.3 Ralph 循环

Ralph 循环是**自我引用**的开发模式，AI 持续工作直到任务验证完成：

```bash
ralph: 重构 auth 模块
```

---

## 12. 配置与定制

### 12.1 完整配置示例

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/master/assets/oh-my-opencode.schema.json",
  
  "agents": {
    "oracle": {
      "model": "openai/gpt-5.2"
    },
    "Sisyphus": {
      "model": "anthropic/claude-opus-4.5",
      "temperature": 0.3
    }
  },
  
  "categories": {
    "data-science": {
      "model": "anthropic/claude-sonnet-4.5"
    }
  },
  
  "background_task": {
    "defaultConcurrency": 5,
    "providerConcurrency": {
      "anthropic": 3,
      "openai": 5,
      "google": 10
    }
  },
  
  "disabled_hooks": ["comment-checker"],
  "disabled_agents": ["multimodal-looker"],
  "disabled_skills": ["playwright"],
  "disabled_mcps": ["websearch"],
  
  "experimental": {
    "aggressive_truncation": false,
    "auto_resume": true,
    "preemptive_compaction": true
  }
}
```

### 12.2 智能体覆盖

```json
{
  "agents": {
    "Sisyphus": {
      "model": "proxy/google.antigravity.claude-sonnet-4.5",
      "temperature": 0.3
    }
  }
}
```

### 12.3 背景任务并发

```json
{
  "background_task": {
    "providerConcurrency": {
      "anthropic": 3,
      "openai": 5
    }
  }
}
```

---

## 13. 实战工作流

### 13.1 日常开发流程

```bash
# 1. 进入项目
cd my-project
opencode

# 2. 初始化（首次）
/init

# 3. 开始工作
> 添加用户注册功能

# 4. 使用 refactor 命令进行重构
/refactor
```

### 13.2 复杂任务流程

```bash
# 1. 使用 ultrawork 启动
ulw 重构整个认证模块

# 2. Sisyphus 自动：
#    - 分析任务
#    - 创建 TODO 列表
#    - 并行委托给专业智能体
#    - 验证结果
```

### 13.3 团队协作流程

```bash
# 1. 项目初始化
/init-deep

# 2. 在团队中共享 AGENTS.md
git add AGENTS.md
git commit -m "docs: add project rules for AI assistants"

# 3. 团队成员克隆后直接使用
opencode
```

### 13.4 研究与探索流程

```bash
# 1. 使用 Explore 智能体
探索这个代码库的结构

# 2. 使用 Librarian 查找文档
Librarian: 查找关于 React Hooks 的使用示例

# 3. 使用 Oracle 咨询
@oracle 这个架构设计有什么问题？
```

---

## 14. 常见问题与技巧

### 14.1 常见问题

**Q: 如何选择 Sisyphus 和直接 Agent Mode？**
- 简单任务 → 直接 Agent Mode
- 复杂多步骤任务 → Sisyphus

**Q: 自定义智能体不生效？**
- 检查文件名：`oh-my-opencode.json`（不是 `.jsonc`）
- 检查路径：`.opencode/agents/` 或 `~/.config/opencode/agents/`

**Q: 上下文太长怎么办？**
- 启用 `preemptive_compaction`
- 使用 `/init-deep` 生成精简的 AGENTS.md

### 14.2 最佳实践

1. **明确任务描述**
   - ❌ "Set up testing"
   - ✅ "Set up Jest with TypeScript support, add coverage reporting"

2. **使用合适的智能体**
   - 架构问题 → Oracle
   - 代码探索 → Explore/Librarian
   - 复杂任务 → Sisyphus

3. **充分利用 Hooks**
   - 启用 `todo-continuation-enforcer` 确保任务完成
   - 使用 `preemptive-compaction` 管理上下文

4. **善用 Category**
   - 前端任务 → `visual-engineering`
   - 复杂逻辑 → `ultrabrain`

### 14.3 性能优化

| 场景 | 优化方法 |
|------|----------|
| 高并发需求 | 增加 `background_task.defaultConcurrency` |
| 节省 Token | 启用 `aggressive_truncation` |
| 加速思考 | 使用 `claude-sonnet-4.5` 替代 Opus |
| 本地运行 | 配置 Ollama 端点 |

---

## 附录

### A. 命令速查表

| 命令 | 功能 |
|------|------|
| `opencode` | 启动 OpenCode |
| `opencode -q "prompt"` | 非交互模式 |
| `/init` | 初始化项目 |
| `/agent on/off` | Agent 模式开关 |
| `/refactor` | 智能重构 |
| `/ulw-loop` | Ultrawork 循环 |
| `@oracle` | 调用 Oracle 智能体 |

### B. 资源链接

- **OpenCode 官网**: https://opencode.ai
- **OpenCode 文档**: https://opencode.ai/docs
- **GitHub**: https://github.com/opencode-ai/opencode
- **Oh My OpenCode**: https://ohmyopencode.com
- **Oh My OpenCode GitHub**: https://github.com/code-yeongyu/oh-my-openagent

### C. 版本要求

- OpenCode: 1.0.133+
- Node.js: 16+（如使用 npm 安装）
- 推荐终端: WezTerm, Alacritty, Ghostty

---

*本文档由 AI 生成，仅供团队内部技术分享使用。最后更新：2026年3月*
