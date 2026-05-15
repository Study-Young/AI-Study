# Claude Code 学习指南

> Claude Code — Anthropic 官方推出的命令行 AI 编码代理

## 📖 概述

Claude Code 是 Anthropic 开发的一款基于终端的 AI 编码助手。它直接运行在您的终端中，能够：

- **阅读和理解**您的项目代码和结构
- **编写和修改**代码文件
- **执行命令**和运行脚本
- **审查代码质量**和安全漏洞
- **自动生成文档和测试**
- **集成外部服务**（数据库、GitHub、API 等）

## 📚 文档目录

### 安装指南

| 文档 | 说明 |
|------|------|
| [Linux 安装](installation/Linux.md) | Ubuntu/Debian/Fedora/CentOS 安装教程 |
| [macOS 安装](installation/macOS.md) | macOS 安装教程（Homebrew/nvm） |
| [Windows 安装](installation/Windows.md) | WSL 2 和 Git Bash 安装教程 |

### 使用指南

| 文档 | 说明 |
|------|------|
| [快速入门](usage/quick-start.md) | Hello World 示例和基本工作流程 |
| [交互模式](usage/interactive-mode.md) | REPL 使用、斜杠命令、Tmux 集成、快捷键 |
| [打印模式](usage/print-mode.md) | `-p` 参数、JSON 输出、流式输出、管道输入 |
| [常用参数](usage/common-flags.md) | 所有命令行参数的详细参考 |

### 进阶主题

| 文档 | 说明 |
|------|------|
| [自定义子代理](advanced/agents.md) | 创建和使用专用 AI 助手 |
| [钩子系统](advanced/hooks.md) | PreToolUse、PostToolUse、Stop 钩子 |
| [MCP 集成](advanced/mcp-integration.md) | 连接外部工具和数据源 |
| [设置与配置](advanced/settings.md) | 配置层级、CLAUDE.md、规则目录 |

### 实战示例

| 文档 | 说明 |
|------|------|
| [代码审查](advanced/examples/code-review.md) | 使用 Claude Code 进行代码审查 |
| [并行任务处理](advanced/examples/parallel-tasks.md) | 批量处理和并行任务策略 |

## 🚀 快速安装

```bash
# 前提条件：Node.js 18+
npm install -g @anthropic-ai/claude-code

# 认证（推荐 OAuth）
claude login

# 验证安装
claude --version
claude doctor
```

## 🔧 基本用法

```bash
# 交互模式（持续对话）
claude

# 打印模式（单次任务）
claude -p "解释这个项目的架构"

# 管道模式
cat error.log | claude -p "分析这些错误"

# 指定模型
claude --model claude-sonnet-4-20250514 -p "重构这段代码"
```

## 📂 文件结构

```
AI-Learning/Claude-Code/
├── README.md                        # 本文档
├── installation/
│   ├── Linux.md                     # Linux 安装指南
│   ├── macOS.md                     # macOS 安装指南
│   └── Windows.md                   # Windows 安装指南
├── usage/
│   ├── quick-start.md               # 快速入门
│   ├── interactive-mode.md          # 交互模式
│   ├── print-mode.md                # 打印模式
│   └── common-flags.md              # 常用参数
└── advanced/
    ├── agents.md                    # 自定义子代理
    ├── hooks.md                     # 钩子系统
    ├── mcp-integration.md           # MCP 集成
    ├── settings.md                  # 设置与配置
    └── examples/
        ├── code-review.md           # 代码审查示例
        └── parallel-tasks.md        # 并行任务处理示例
```

## 🎯 学习路径

### 初级

1. 根据您的平台选择安装指南，完成 Claude Code 安装
2. 阅读[快速入门](usage/quick-start.md)，了解基本用法
3. 尝试使用交互模式完成简单的编码任务
4. 学习[打印模式](usage/print-mode.md)，掌握一次性任务处理

### 中级

1. 阅读[常用参数](usage/common-flags.md)，学习高级命令行选项
2. 掌握[交互模式](usage/interactive-mode.md)的斜杠命令和快捷键
3. 学习[设置与配置](advanced/settings.md)，为项目创建 CLAUDE.md
4. 参考[代码审查](advanced/examples/code-review.md)示例，在项目中使用

### 高级

1. 学习创建[自定义子代理](advanced/agents.md)，定制专用 AI 助手
2. 配置[钩子系统](advanced/hooks.md)，实现安全控制和自动化
3. 集成 [MCP 服务](advanced/mcp-integration.md)，连接数据库和外部 API
4. 使用[并行任务](advanced/examples/parallel-tasks.md)策略，提升工作效率

## 💡 使用技巧

- **项目指令**：在项目根目录创建 `.claude/CLAUDE.md` 或 `CLAUDE.md`，让 Claude Code 了解您的项目规范和约定
- **快捷切换**：使用 `/agent` 命令在子代理之间快速切换，让不同的 AI 专家处理不同的任务
- **安全控制**：使用 PreToolUse 钩子限制文件访问和命令执行，确保安全
- **批量处理**：结合 shell 脚本和 `-p` 参数，实现批量代码处理和审查
- **CI/CD 集成**：将 Claude Code 集成到 GitHub Actions 等 CI/CD 流程中，自动进行代码审查

## 🔗 相关资源

- [Anthropic 官方网站](https://www.anthropic.com/)
- [Claude API 文档](https://docs.anthropic.com/)
- [MCP 协议文档](https://modelcontextprotocol.io/)
- [npm 包地址](https://www.npmjs.com/package/@anthropic-ai/claude-code)

## 📝 许可

本文档仅供学习参考。Claude Code 是 Anthropic 的商标。

---

*最后更新时间：2025 年 5 月*
