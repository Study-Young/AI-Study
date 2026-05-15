# Hermes Agent

> 由 Nous Research 构建的开源 AI 代理框架

Hermes Agent 是一个功能强大的开源 AI 代理（Agent）框架，支持 20 多种大语言模型（LLM）提供商，可在多平台上运行（终端、Telegram、Discord、Slack、WhatsApp 等）。它具备技能系统、持久记忆、定时任务、子代理委派和 MCP 客户端等高级功能。

## 主要特性

- **多模型支持** — 支持 OpenRouter、Anthropic、OpenAI、DeepSeek、Google Gemini 等 20+ LLM 提供商
- **多平台运行** — 终端 CLI、Telegram、Discord、Slack、WhatsApp、Signal 等
- **技能系统** — 以 Markdown 文件形式存储的可复用程序，让 AI 学会执行特定任务
- **持久记忆** — 跨会话持久记忆，支持多种后端存储（JSON、SQLite、PostgreSQL 等）
- **定时任务** — 内置 Cron 调度系统，支持一次性与周期性任务
- **子代理委派** — 通过 `delegate_task` 函数生成子代理，支持单次与批量委派
- **MCP 客户端** — 原生支持 Model Context Protocol（MCP），可连接多种外部工具
- **消息网关** — 统一的平台集成层，轻松对接各种即时通讯平台

## 快速安装

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装后运行设置向导：

```bash
hermes setup
```

## 目录

### 安装指南
- [Linux / WSL](installation/Linux-WSL.md)
- [macOS](installation/macOS.md)
- [Windows](installation/Windows.md)

### 使用指南
- [快速上手](usage/quick-start.md)
- [CLI 参考](usage/cli-reference.md)
- [斜杠命令](usage/slash-commands.md)
- [多平台部署](usage/multi-platform.md)

### 高级功能
- [技能系统](advanced/skills.md)
- [记忆系统](advanced/memory.md)
- [定时任务](advanced/cron.md)
- [子代理委派](advanced/delegation.md)
- [MCP 协议](advanced/mcp.md)
- [消息网关](advanced/gateway.md)

### 实践示例
- [多代理协作](advanced/examples/multi-agent.md)
- [自动化工作流](advanced/examples/automation.md)

## 配置

配置文件位于 `~/.hermes/config.yaml`，API 密钥位于 `~/.hermes/.env`。

## 命令行

| 命令 | 说明 |
|------|------|
| `hermes chat` | 启动交互式聊天 |
| `hermes setup` | 运行设置向导 |
| `hermes config` | 查看/编辑配置 |
| `hermes doctor` | 诊断安装问题 |
| `hermes model` | 切换或查看模型 |

## 许可证

本项目采用 [Apache 2.0 许可证](https://github.com/NousResearch/hermes-agent/blob/main/LICENSE) 开源。
