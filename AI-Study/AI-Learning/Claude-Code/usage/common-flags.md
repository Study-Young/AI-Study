# 常用参数参考

本文档列出 Claude Code 所有可用的命令行参数（Flags），包括详细说明和使用示例。

## 参数一览表

| 参数 | 简写 | 类型 | 默认值 | 说明 |
|------|------|------|--------|------|
| `--print` | `-p` | string | - | 打印模式，执行单次请求后退出 |
| `--model` | 无 | string | 最新模型 | 指定使用的 Claude 模型 |
| `--max-turns` | 无 | number | 100 | 最大交互轮数 |
| `--allowedTools` | 无 | string | 全部 | 限制 Claude 可以使用的工具（逗号分隔） |
| `--output-format` | 无 | string | `text` | 输出格式：`text` 或 `json` |
| `--verbose` | 无 | boolean | `false` | 显示详细执行日志 |
| `--no-stream` | 无 | boolean | `false` | 禁用流式输出 |
| `--configPath` | 无 | string | - | 指定配置文件路径 |
| `--mcp-config` | 无 | string | - | 指定 MCP 服务器配置文件路径 |
| `--agents` | 无 | string | - | 使用自定义子代理配置（JSON） |
| `--max-tokens` | 无 | number | 4096 | 每次响应的最大 token 数量 |
| `--temperature` | 无 | number | 1.0 | 模型温度参数（0-2） |
| `--system-prompt` | 无 | string | - | 自定义系统提示词 |
| `--help` | `-h` | boolean | `false` | 显示帮助信息 |
| `--version` | `-v` | boolean | `false` | 显示版本信息 |

## 参数详解

### `-p, --print <text>`

打印模式，执行单次请求后立即退出。

```bash
# 基本用法
claude -p "解释 JavaScript 闭包"

# 长格式
claude --print "解释 JavaScript 闭包"

# 结合其他参数
claude -p "编写单元测试" --output-format json
```

### `--model <model_name>`

指定使用的 Claude 模型。

```bash
# 使用特定模型
claude --model claude-sonnet-4-20250514

# 使用最新模型
claude --model claude-latest

# 在交互模式中查看当前模型
claude
❯ /model
```

可用的模型选项：

| 模型 | 特点 |
|------|------|
| `claude-sonnet-4-20250514` | Sonnet 4，平衡速度与能力，推荐用于开发 |
| `claude-opus-4-20250514` | Opus 4，最强能力，适合复杂任务 |
| `claude-haiku-3-5-20241022` | Haiku 3.5，快速轻量，适合简单任务 |
| `claude-latest` | 自动选择最新可用模型 |

### `--max-turns <number>`

控制对话的最大轮数。每轮（turn）包括 Claude 的一次响应和可选的一次工具调用。

```bash
# 限制对话轮数
claude -p "重构这个大型项目" --max-turns 20

# 交互模式中限制
claude --max-turns 50
```

### `--allowedTools <tool_names>`

限制 Claude 可以使用的工具。多个工具用逗号分隔。

```bash
# 只允许读取和写入文件
claude -p "审查代码" --allowedTools Read,Write

# 允许所有工具（默认）
claude -p "部署应用" --allowedTools All

# 禁止危险操作
claude -p "查看配置" --allowedTools Read
```

可用工具列表：

| 工具 | 说明 |
|------|------|
| `Read` | 读取文件内容 |
| `Write` | 创建和写入文件 |
| `Edit` | 编辑文件（补丁式修改） |
| `Bash` | 执行 shell 命令 |
| `ReadDir` | 读取目录列表 |
| `Glob` | 搜索文件名 |
| `Grep` | 搜索文件内容 |
| `All` | 允许所有工具 |

### `--output-format <format>`

指定输出格式。

```bash
# 文本格式（默认）
claude -p "分析数据" --output-format text

# JSON 格式（适合程序处理）
claude -p "分析数据" --output-format json
```

### `--verbose`

显示详细执行日志，包括每一步的分析和决策过程。

```bash
claude -p "调试这个错误" --verbose
```

详细输出示例：

```
[2024-01-15 10:30:22] 开始处理请求
[2024-01-15 10:30:22] 读取文件: src/app.js (345 bytes)
[2024-01-15 10:30:23] 调用工具: grep(pattern="error", path="src/")
[2024-01-15 10:30:24] 分析错误模式...
[2024-01-15 10:30:25] 生成修复方案...
[2024-01-15 10:30:26] 编辑文件: src/app.js (行 42-56)
```

### `--no-stream`

禁用流式输出。默认情况下，Claude Code 会实时流式显示输出内容。

```bash
# 禁用流式输出（等全部完成后一次性显示）
claude -p "生成报告" --no-stream
```

### `--configPath <path>`

指定自定义配置文件路径。

```bash
# 使用项目特定配置
claude --configPath ./config/claude.json

# 使用绝对路径
claude --configPath /home/user/.claude/config.json
```

### `--mcp-config <path>`

指定 MCP（Model Context Protocol）服务器配置文件。用于连接外部工具和数据源。

```bash
claude --mcp-config ./mcp-servers.json
```

### `--agents <json_string>`

指定自定义子代理配置。使用 JSON 字符串或文件路径。

```bash
# JSON 字符串方式
claude --agents '{"reviewer": {"description": "代码审查专家", "prompt": "..."}}'

# 文件方式（包含路径）
claude --agents ./agents-config.json
```

### `--max-tokens <number>`

限制每次 Claude 响应的最大 token 数量。

```bash
# 简短响应
claude -p "给我一个简短的回答" --max-tokens 100

# 长响应
claude -p "详细分析" --max-tokens 8192
```

### `--temperature <number>`

控制输出的随机性。值越低越确定，值越高越有创造性。

```bash
# 确定性输出（适合代码生成）
claude -p "写一个排序算法" --temperature 0.1

# 创造性输出
claude -p "给我想一些项目创意" --temperature 1.5
```

### `--system-prompt <text>`

自定义系统提示词，覆盖默认的 Claude Code 行为。

```bash
# 自定义角色
claude -p "审查这段代码" --system-prompt "你是一位专业的 Python 安全审计专家"

# 指定输出语言
claude --system-prompt "请始终用中文回答" --print "解释 SOLID 原则"
```

## 参数组合示例

### 生产环境脚本

```bash
claude -p "检查代码质量和安全漏洞" \
       --model claude-sonnet-4-20250514 \
       --max-turns 30 \
       --output-format json \
       --allowedTools Read,Grep \
       --temperature 0.1 \
       --verbose
```

### 快速开发辅助

```bash
claude -p "为这个函数添加单元测试" \
       --max-turns 10 \
       --allowedTools Read,Write,Edit,Bash
```

### CI/CD 集成

```bash
claude -p "生成变更日志" \
       --model claude-haiku-3-5-20241022 \
       --output-format json \
       --max-tokens 2000 \
       --no-stream
```

## 环境变量

以下环境变量可以替代对应的命令行参数：

| 环境变量 | 对应参数 | 说明 |
|----------|----------|------|
| `ANTHROPIC_API_KEY` | - | API 密钥（必需） |
| `CLAUDE_MODEL` | `--model` | 默认模型 |
| `CLAUDE_MAX_TOKENS` | `--max-tokens` | 最大 token 数 |
| `CLAUDE_TEMPERATURE` | `--temperature` | 温度参数 |
| `CLAUDE_ALLOWED_TOOLS` | `--allowedTools` | 允许的工具 |
| `CLAUDE_CONFIG_PATH` | `--configPath` | 配置文件路径 |
| `HTTP_PROXY` | - | HTTP 代理 |
| `HTTPS_PROXY` | - | HTTPS 代理 |

```bash
# 使用环境变量的示例
export CLAUDE_MODEL=claude-sonnet-4-20250514
export CLAUDE_MAX_TOKENS=4096
claude -p "处理任务"
```
