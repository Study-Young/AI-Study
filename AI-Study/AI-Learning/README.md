# AI 学习模块 (AI-Learning)

> 系统性学习当下最热门的 AI 工程工具 —— 从安装到精通，从入门到实战。

---

## 一、模块定位

AI 工程化工具正在快速发展，CLI 编码智能体已经成为开发者工作流中不可或缺的一部分。本模块旨在提供 **三大主流 AI 编码工具** 的完整学习资源，包括：

- **跨平台安装指南** — Linux / macOS / Windows 全覆盖
- **详细使用方法** — 从快速入门到高级功能逐级展开
- **进阶教程与实战示例** — 真实场景下的最佳实践

无论你是 AI 工具的初学者还是寻求深入掌握的高级用户，这里都有适合你的内容。

---

## 二、工具总览

### 1. 🤖 [Claude Code](./Claude-Code/README.md)

> **提供商：Anthropic** · 闭源 · 命令行交互式编码智能体

Claude Code 是 Anthropic 推出的 CLI 编程智能体，直接在终端中运行。它能够理解整个代码库结构、执行 Shell 命令、编辑文件，并提供交互式 REPL 会话体验。

**核心特性：**
- 多文件上下文感知的代码理解
- 交互式 / 非交互式（Print）双模式
- 自定义子 Agent 系统（任务委派）
- MCP 服务器集成扩展工具链
- CLAUDE.md 项目级配置管理
- 钩子系统（PreToolUse / PostToolUse）

**适用场景：** 日常编码辅助、代码审查、重构、跨文件修改、CI/CD 集成

| 平台 | 安装方式 | 文档入口 |
|------|---------|---------|
| Linux | npm | [安装指南](./Claude-Code/installation/Linux.md) |
| macOS | npm / brew | [安装指南](./Claude-Code/installation/macOS.md) |
| Windows | npm (WSL) | [安装指南](./Claude-Code/installation/Windows.md) |

---

### 2. 🧠 [Hermes Agent](./Hermes-Agent/README.md)

> **提供商：Nous Research** · 开源 (Apache 2.0) · 全功能 CLI AI 智能体

Hermes Agent 是 Nous Research 开发的开源 CLI AI 智能体，支持多平台接入和丰富的扩展能力。它不仅是一个编码助手，更是一个可承载多种角色的通用 AI 智能体平台。

**核心特性：**
- 多平台接入（终端 / Telegram / Discord / Slack）
- 技能系统（Skills）—— 可插拔功能模块
- 持久化记忆（Memory）—— 跨会话知识保留
- 定时任务（Cron）—— 自动化定期执行
- 任务委派（Delegation）—— Agent 间协作
- MCP 服务器集成
- 消息网关（Gateway）—— 统一消息路由

**适用场景：** 编码辅助、自动化工作流、多 Agent 协作、定时资讯采集、智能客服

| 平台 | 安装方式 | 文档入口 |
|------|---------|---------|
| Linux / WSL | pip / uv | [安装指南](./Hermes-Agent/installation/Linux-WSL.md) |
| macOS | pip / uv | [安装指南](./Hermes-Agent/installation/macOS.md) |
| Windows | pip / uv | [安装指南](./Hermes-Agent/installation/Windows.md) |

---

### 3. ⚡ [OpenCode](./OpenCode/README.md)

> **提供商：社区驱动** · 开源 (MIT) · 提供商无关的 AI 编码代理

OpenCode 是一个开源、提供商无关的 AI 编码代理，提供直观的终端用户界面（TUI）和强大的 CLI。你可以自由选择底层 AI 模型 —— OpenRouter、Anthropic、OpenAI、Google Gemini 等。

**核心特性：**
- 提供商无关架构 —— 自由切换模型
- 交互式 TUI 模式 —— 全功能终端界面
- 一次性命令模式 —— `opencode run` 快速任务执行
- 自定义 Agent 系统 —— 创建可复用的专用 Agent
- PR 审核工作流 —— 自动化代码审查
- 会话管理与成本追踪
- 并行工作模式（git worktree）

**适用场景：** 多模型切换对比、代码审查流水线、并行任务处理、快速原型开发

| 平台 | 安装方式 | 文档入口 |
|------|---------|---------|
| Linux | npm / brew | [安装指南](./OpenCode/installation/Linux.md) |
| macOS | npm / brew | [安装指南](./OpenCode/installation/macOS.md) |
| Windows | npm | [安装指南](./OpenCode/installation/Windows.md) |

---

## 三、快速对比

| 维度 | Claude Code | Hermes Agent | OpenCode |
|------|------------|-------------|---------|
| **开源** | ❌ 闭源 | ✅ Apache 2.0 | ✅ MIT |
| **提供商绑定** | Anthropic 独占 | 多提供商支持 | 多提供商支持 |
| **交互模式** | REPL / Print | 交互式 CLI | TUI / CLI |
| **支持平台** | 终端 | 终端 + Telegram/Discord/Slack | 终端 |
| **技能系统** | ❌ 无 | ✅ 可插拔技能 | ❌ 无 |
| **记忆持久化** | ✅ 会话日志 | ✅ 结构化记忆 | ✅ 会话管理 |
| **子 Agent** | ✅ 子 Agent | ✅ 任务委派 | ✅ 自定义 Agent |
| **MCP 支持** | ✅ | ✅ | ❌ 暂无 |
| **多平台消息** | ❌ | ✅ Telegram/Discord/Slack | ❌ |
| **学习曲线** | 中等 | 较陡（功能丰富） | 平缓（简洁专注） |

---

## 四、学习路径推荐

```
初学者 ──▶ Claude Code 或 OpenCode（简洁快速上手）
               │
               ▼
          掌握基础后
               │
               ▼
进阶用户 ──▶ Hermes Agent（功能最全面，适合自动化）
               │
               ▼
          多工具协同
               │
               ▼
高级用户 ──▶ 根据场景灵活选用 / 多 Agent 组合使用
```

### 推荐的学习步骤

对于 **每个工具**，按以下顺序阅读文档：

1. **安装指南** — 选择对应平台的安装文档，完成工具部署
2. **快速入门** — 运行第一个命令，体验基本功能
3. **使用详解** — 深入理解各模式和工作方式
4. **进阶功能** — 掌握高级特性和自定义配置
5. **实战示例** — 参考实际场景的最佳实践

---

## 五、目录结构总览

```
AI-Learning/
├── README.md                         ← 本文件（模块总览）
│
├── Claude-Code/                      # Claude Code 完整文档
│   ├── README.md                     # 工具简介
│   ├── installation/                 # 安装指南（Linux/macOS/Windows）
│   ├── usage/                        # 使用方法（入门/交互/打印/参数）
│   └── advanced/                     # 进阶功能（Agent/钩子/MCP/配置/示例）
│
├── Hermes-Agent/                     # Hermes Agent 完整文档
│   ├── README.md                     # 工具简介
│   ├── installation/                 # 安装指南（Linux-WSL/macOS/Windows）
│   ├── usage/                        # 使用方法（入门/CLI/斜杠命令/多平台）
│   └── advanced/                     # 进阶功能（技能/记忆/定时/委派/MCP/网关/示例）
│
└── OpenCode/                         # OpenCode 完整文档
    ├── README.md                     # 工具简介
    ├── installation/                 # 安装指南（Linux/macOS/Windows）
    ├── usage/                        # 使用方法（入门/一次性/TUI/配置）
    └── advanced/                     # 进阶功能（自定义Agent/PR审核/并行工作）
```

---

## 六、环境要求

| 依赖 | 最低版本 | 推荐版本 | 用途 |
|------|---------|---------|------|
| Node.js | ≥ 18.x | 20.x LTS | Claude Code / OpenCode 运行环境 |
| Python | ≥ 3.10 | 3.12 | Hermes Agent 运行环境 |
| Git | ≥ 2.30 | 2.40+ | 版本控制与协作 |
| 终端 | ANSI + Unicode | tmux/kitty | 最佳交互体验 |

---

## 七、贡献与反馈

本项目正处于持续建设中。如果你在使用过程中发现文档错误、链接失效或有改进建议，欢迎：

- 提交 Issue 或 Pull Request
- 通过 Hermes Agent 直接与项目维护者交流
- 参考文档结构为新工具添加学习资源

---

*学无止境，AI 赋能。*
