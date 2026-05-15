# OpenCode — 开源、提供商无关的 AI 编码代理

[![npm 版本](https://img.shields.io/npm/v/opencode-ai)](https://www.npmjs.com/package/opencode-ai)
[![许可证](https://img.shields.io/github/license/anomalyco/opencode)](https://github.com/anomalyco/opencode/blob/main/LICENSE)
[![下载量](https://img.shields.io/npm/dm/opencode-ai)](https://www.npmjs.com/package/opencode-ai)

**OpenCode** 是一个开源、提供商无关的 AI 编码代理，提供直观的终端用户界面（TUI）和强大的命令行界面（CLI）。它支持多种 AI 提供商，包括 OpenRouter、Anthropic、OpenAI 等，让您自由选择最适合您需求的模型。

## ✨ 特性

- **提供商无关** — 支持 OpenRouter、Anthropic Claude、OpenAI GPT、Google Gemini 等
- **交互式 TUI 模式** — 全功能终端界面，支持会话管理、成本追踪和实时流式输出
- **一次性命令模式** — `opencode run '提示词'` 快速完成单个任务
- **自定义代理系统** — 创建和共享可复用的自定义 AI 代理
- **PR 审核工作流** — 自动化的代码审查流程
- **会话管理** — 保存、加载和恢复会话
- **成本追踪** — 实时监控 API 使用情况和费用
- **并行工作** — 通过 git worktree 实现并行任务处理
- **上下文感知** — 自动理解项目结构和代码库

## 🚀 快速安装

```bash
# npm（推荐）
npm i -g opencode-ai@latest

# Homebrew（macOS / Linux）
brew install anomalyco/tap/opencode

# 或从 opencode.ai 下载二进制文件
```

## 📖 快速开始

```bash
# 认证
opencode auth login

# 一次性任务模式
opencode run '解释这个项目的结构'

# 交互式 TUI 模式
opencode
```

## 📚 文档索引

### 安装指南
- [Linux 安装](installation/Linux.md)
- [macOS 安装](installation/macOS.md)
- [Windows 安装](installation/Windows.md)

### 使用指南
- [快速入门](usage/quick-start.md)
- [一次性命令模式](usage/one-shot.md)
- [交互式 TUI 模式](usage/interactive-tui.md)
- [配置指南](usage/configuration.md)

### 高级主题
- [自定义代理](advanced/custom-agents.md)
- [PR 审核](advanced/pr-review.md)
- [并行工作示例](advanced/examples/parallel-work.md)

## 🔐 认证

```bash
# 交互式登录（推荐）
opencode auth login

# 或设置环境变量
export OPENAI_API_KEY=sk-xxx
export ANTHROPIC_API_KEY=sk-ant-xxx
export OPENROUTER_API_KEY=sk-or-xxx
```

## ⚙️ 配置

OpenCode 支持通过 `opencode.toml`、`opencode.yaml` 或 `opencode.json` 配置文件进行自定义。配置文件可放在项目根目录或用户主目录。

```toml
# opencode.toml 示例
[provider]
name = "openrouter"
model = "anthropic/claude-sonnet-20241022"

[context]
max_depth = 3
include_patterns = ["src/**/*.py", "src/**/*.ts"]

[cost]
budget_limit = 10.0
```

## 🤝 贡献

欢迎贡献！请查看 [GitHub 仓库](https://github.com/anomalyco/opencode) 了解详情。

## 📄 许可证

MIT
