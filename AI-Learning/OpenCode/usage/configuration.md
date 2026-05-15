# 配置指南

OpenCode 提供灵活的配置系统，支持多种配置格式和丰富的配置选项。

## 配置文件优先级

OpenCode 按照以下优先级加载配置（高优先级覆盖低优先级）：

1. **命令行参数** — `--model`, `--provider` 等
2. **项目级配置文件** — 当前项目目录下的 `opencode.toml` / `opencode.yaml` / `opencode.json`
3. **用户级全局配置** — `~/.config/opencode/config.toml`
4. **环境变量** — `OPENAI_API_KEY`, `OPENROUTER_API_KEY` 等
5. **默认配置** — 内置的合理默认值

## 支持的配置格式

### TOML（推荐）

```toml
# opencode.toml
[provider]
name = "openrouter"
model = "anthropic/claude-sonnet-20241022"
```

### YAML

```yaml
# opencode.yaml
provider:
  name: openrouter
  model: anthropic/claude-sonnet-20241022
```

### JSON

```json
{
  "provider": {
    "name": "openrouter",
    "model": "anthropic/claude-sonnet-20241022"
  }
}
```

## 快速初始化

```bash
# 在当前项目目录生成默认配置文件
opencode config init

# 查看当前生效的完整配置
opencode config show

# 查看配置文件的存储位置
opencode config path
```

## 完整配置选项

### 提供商配置

```toml
[provider]
# AI 提供商名称
# 可选: openrouter, anthropic, openai, google, ollama, custom
name = "openrouter"

# 模型名称
# OpenRouter 示例: "anthropic/claude-sonnet-20241022", "openai/gpt-4o"
# Anthropic 示例: "claude-sonnet-4-20250514"
# OpenAI 示例: "gpt-4o", "gpt-4-turbo"
model = "anthropic/claude-sonnet-20241022"

# API 基础 URL（可选，用于自定义端点）
# base_url = "https://api.openai.com/v1"

# API 密钥（也可以使用环境变量）
# api_key = "sk-xxx"

# 最大重试次数
max_retries = 3

# 请求超时时间（秒）
timeout = 60
```

### 模型参数配置

```toml
[model]
# 生成温度（0.0 - 1.0）
# 较低值使输出更确定，较高值增加创造性
temperature = 0.7

# 最大输出 token 数
max_tokens = 4096

# Top-p 采样参数
top_p = 0.9

# 频率惩罚（-2.0 - 2.0）
frequency_penalty = 0.0

# 存在惩罚（-2.0 - 2.0）
presence_penalty = 0.0

# 停止序列
# stop = ["\n\n", "```"]
```

### 上下文配置

```toml
[context]
# 项目扫描的最大目录深度
max_depth = 5

# 最大上下文文件数
max_files = 50

# 包含模式（仅扫描匹配的文件）
include_patterns = [
  "src/**/*.ts",
  "src/**/*.tsx",
  "src/**/*.py",
  "src/**/*.js",
  "package.json",
  "tsconfig.json",
]

# 排除模式（跳过匹配的文件）
exclude_patterns = [
  "node_modules/**",
  "dist/**",
  ".git/**",
  "*.min.js",
  "*.generated.*",
]

# 自动包含 .env.example（而非 .env）
include_env_example = true

# 二进制文件大小限制（字节）
binary_max_size = 1048576  # 1MB
```

### 成本控制配置

```toml
[cost]
# 单次会话预算上限（美元）
budget_limit = 10.0

# 每日预算上限（美元）
daily_budget = 50.0

# 超过预算时的行为
# "warn" - 仅警告
# "stop" - 停止生成
# "ask"  - 询问用户
over_budget_action = "ask"

# 显示 token 使用明细
show_token_usage = true

# 记录成本历史
track_history = true
```

### 界面配置

```toml
[ui]
# TUI 主题
# 可选: "default", "dark", "light", "solarized-dark", "solarized-light", "nord"
theme = "default"

# 是否启用 emoji 图标
show_emoji = true

# 是否显示时间戳
show_timestamps = true

# 消息显示的最大宽度（字符数）
message_width = 80

# 语法高亮主题
syntax_theme = "one-dark"

# 语言设置
# 可选: "en", "zh-CN", "zh-TW", "ja", "ko"
language = "zh-CN"
```

### 会话管理配置

```toml
[session]
# 自动保存间隔（秒）
auto_save_interval = 30

# 最大保存会话数
max_sessions = 100

# 会话存储目录
# store_dir = "~/.local/share/opencode/sessions"

# 导出格式
# 可选: "markdown", "json", "txt"
export_format = "markdown"
```

### Git 集成配置

```toml
[git]
# 提交消息风格
# 可选: "conventional", "simple", "detailed"
commit_style = "conventional"

# PR 审核配置
[git.pr_review]
# 审核自动触发条件: "always", "on-open", "manual"
auto_review = "on-open"

# 审核重点: "security", "performance", "style", "all"
focus = "all"

# 最大审查文件数
max_files = 20
```

### 自定义代理配置

```toml
[agents]
# 自定义代理定义文件目录
# agents_dir = "~/.config/opencode/agents"
```

## 环境变量

除了配置文件，OpenCode 也支持通过环境变量进行配置：

### API 密钥

| 环境变量 | 说明 |
|----------|------|
| `OPENAI_API_KEY` | OpenAI API 密钥 |
| `ANTHROPIC_API_KEY` | Anthropic API 密钥 |
| `OPENROUTER_API_KEY` | OpenRouter API 密钥 |
| `GOOGLE_API_KEY` | Google Gemini API 密钥 |
| `OLLAMA_HOST` | Ollama 服务地址（默认 http://localhost:11434） |

### 运行时配置

| 环境变量 | 说明 |
|----------|------|
| `OPENCODE_PROVIDER` | 默认提供商 |
| `OPENCODE_MODEL` | 默认模型 |
| `OPENCODE_TEMPERATURE` | 默认温度值 |
| `OPENCODE_MAX_TOKENS` | 默认最大 token 数 |
| `OPENCODE_BUDGET_LIMIT` | 预算上限 |
| `OPENCODE_THEME` | TUI 主题 |
| `OPENCODE_LANGUAGE` | 界面语言 |
| `OPENCODE_CONTEXT_DEPTH` | 上下文深度 |
| `OPENCODE_DEBUG` | 启用调试输出（`true`/`false`） |
| `OPENCODE_LOG_LEVEL` | 日志级别: `debug`, `info`, `warn`, `error` |

### 代理配置

```bash
# HTTP 代理
export HTTP_PROXY="http://127.0.0.1:7890"
export HTTPS_PROXY="http://127.0.0.1:7890"
export NO_PROXY="localhost,127.0.0.1"
```

## 项目级配置示例

### 小型前端项目

```toml
[provider]
name = "openrouter"
model = "openai/gpt-4o"

[context]
include_patterns = ["src/**/*.{ts,tsx,css}", "package.json"]
max_depth = 3

[ui]
language = "zh-CN"
```

### 大型 Python 后端项目

```toml
[provider]
name = "anthropic"
model = "claude-sonnet-4-20250514"

[context]
max_depth = 8
include_patterns = ["src/**/*.py", "tests/**/*.py", "pyproject.toml"]
exclude_patterns = ["venv/**", "__pycache__/**", "*.pyc"]
max_files = 80

[cost]
budget_limit = 20.0
over_budget_action = "ask"

[git.pr_review]
focus = "security"
```

### 使用 Ollama（本地模型）

```toml
[provider]
name = "ollama"
model = "codellama:34b"
base_url = "http://localhost:11434"
max_retries = 2
timeout = 120

[model]
temperature = 0.3
max_tokens = 2048

[cost]
budget_limit = 0  # 本地模型免费
```

## 常见问题

### 配置文件在哪？

```bash
# 项目级配置
# 位于项目根目录: ./opencode.toml

# 用户级全局配置
# Linux: ~/.config/opencode/config.toml
# macOS: ~/.config/opencode/config.toml
# Windows: %USERPROFILE%\.config\opencode\config.toml
```

### 如何临时覆盖配置？

使用命令行参数：

```bash
opencode run '...' --provider openai --model gpt-4o --temperature 0.3
```

### 配置未生效？

1. 运行 `opencode config show` 查看当前生效的配置
2. 检查文件格式是否正确（TOML/YAML/JSON）
3. 确认文件在正确的目录（项目根目录 vs 用户目录）
4. 检查是否有更高优先级的配置覆盖了它

## 下一步

- 学习创建 [自定义代理](../advanced/custom-agents.md)
- 探索 [PR 审核工作流](../advanced/pr-review.md)
- 查看 [并行工作示例](../advanced/examples/parallel-work.md)
