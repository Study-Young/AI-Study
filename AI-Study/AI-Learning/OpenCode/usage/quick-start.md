# 快速入门

本文档帮助您快速上手 OpenCode，从安装到完成第一个任务只需几分钟。

## 前提条件

- 已按照相应平台的安装指南完成 OpenCode 安装
- 拥有一个支持的 AI 提供商的 API 密钥

## 第一步：认证

OpenCode 需要与 AI 提供商进行认证。选择以下方式之一：

### 方式一：交互式登录（推荐）

```bash
opencode auth login
```

按提示选择您的提供商并输入 API 密钥。

### 方式二：设置环境变量

```bash
# OpenRouter（推荐，可访问多种模型）
export OPENROUTER_API_KEY="sk-or-xxx"

# OpenAI
export OPENAI_API_KEY="sk-xxx"

# Anthropic
export ANTHROPIC_API_KEY="sk-ant-xxx"
```

> **提示**: 将环境变量添加到您的 shell 配置文件（如 `~/.bashrc`、`~/.zshrc`）中，避免每次重启终端都需要重新设置。

## 第二步：验证安装

```bash
opencode --version
```

如果显示版本号（如 `0.x.x`），则安装成功。

## 第三步：运行你的第一个任务

### 一次性命令模式

快速在终端中运行一次性任务：

```bash
opencode run '查看当前目录下有哪些文件，并简要描述这个项目'
```

OpenCode 会自动分析项目结构并给出回答。

### 交互式 TUI 模式

启动全功能的交互式界面：

```bash
opencode
```

您将看到一个美观的终端界面，可以：
- 在输入框中输入提示词
- 查看 AI 的实时流式回复
- 进行多轮对话
- 管理会话历史

## 常用命令速查

| 命令 | 说明 |
|------|------|
| `opencode` | 启动交互式 TUI 模式 |
| `opencode run '提示词'` | 一次性命令模式 |
| `opencode auth login` | 交互式登录 |
| `opencode auth logout` | 退出登录 |
| `opencode config init` | 初始化配置文件 |
| `opencode config show` | 查看当前配置 |
| `opencode session list` | 列出所有会话 |
| `opencode session load <id>` | 加载指定会话 |
| `opencode cost` | 查看 API 使用费用 |
| `opencode --help` | 查看全部命令帮助 |

## 实际示例

### 代码审查

```bash
opencode run '审查 src/main.py 中的代码，指出潜在问题和改进建议'
```

### 生成代码

```bash
opencode run '创建一个 Python 脚本，读取 CSV 文件并生成数据可视化图表'
```

### 解释代码

```bash
opencode run '解释这个项目的架构设计模式'
```

### 调试帮助

```bash
opencode run '我运行测试时出现 TypeError: undefined is not a function，检查 package.json 和 src/ 目录，帮我找到问题'
```

## 配置自定义模型

OpenCode 默认使用 OpenRouter 上的最佳可用模型。您可以通过配置文件自定义：

```bash
# 创建配置文件
opencode config init
```

编辑生成的配置文件（如 `opencode.toml`）：

```toml
[provider]
name = "anthropic"
model = "claude-sonnet-4-20250514"

[context]
max_depth = 5
```

## 下一步

- 了解更多关于 [一次性命令模式](one-shot.md) 的用法
- 深入了解 [交互式 TUI 模式](interactive-tui.md)
- 查看完整的 [配置选项](configuration.md)
- 学习创建 [自定义代理](../advanced/custom-agents.md)
