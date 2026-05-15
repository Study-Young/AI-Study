# CLI 参考

Hermes Agent 提供丰富的命令行工具，用于管理配置、启动会话和诊断问题。

## 全局选项

以下选项可用于所有命令：

| 选项 | 说明 |
|------|------|
| `--help`, `-h` | 显示帮助信息 |
| `--version`, `-v` | 显示版本号 |
| `--config PATH` | 指定自定义配置文件路径（默认：`~/.hermes/config.yaml`） |
| `--env PATH` | 指定自定义环境变量文件路径（默认：`~/.hermes/.env`） |
| `--verbose` | 输出详细日志 |
| `--quiet` | 静默模式，仅输出关键信息 |

---

## hermes chat

启动交互式聊天会话。这是最常用的命令。

```bash
hermes chat [选项]
```

### 选项

| 选项 | 说明 |
|------|------|
| `--model`, `-m` | 指定使用的模型（如 `gpt-4o`、`claude-sonnet-4-20250514`） |
| `--platform`, `-p` | 指定平台（如 `terminal`、`telegram`、`discord`） |
| `--skill`, `-s` | 启用指定技能（可多次使用） |
| `--no-stream` | 禁用流式输出 |
| `--temperature` | 设置生成温度（0.0-2.0，默认 0.7） |
| `--max-tokens` | 设置最大生成长度 |
| `--system-prompt` | 自定义系统提示词 |
| `--context-file` | 加载上下文文件 |
| `--non-interactive` | 非交互模式（单次问答后退出） |

### 示例

```bash
# 使用默认配置启动
hermes chat

# 指定模型
hermes chat --model claude-sonnet-4-20250514

# 使用低温度值
hermes chat --temperature 0.3

# 非交互单次问答
hermes chat --non-interactive -- prompt "Hello"
```

---

## hermes setup

运行首次设置的交互式向导。

```bash
hermes setup [选项]
```

### 选项

| 选项 | 说明 |
|------|------|
| `--reset` | 重置所有配置 |
| `--provider` | 直接指定 LLM 提供商 |
| `--model` | 直接指定默认模型 |
| `--api-key` | 直接设置 API 密钥 |

### 示例

```bash
# 运行完整设置向导
hermes setup

# 直接指定提供商
hermes setup --provider openai --model gpt-4o --api-key sk-xxx
```

---

## hermes config

查看和修改配置。

```bash
hermes config <子命令> [选项]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| `show` | 显示当前配置 |
| `set <key=value>` | 设置配置项 |
| `get <key>` | 获取特定配置项 |
| `edit` | 在编辑器中打开配置文件 |
| `list` | 列出所有配置项 |

### 示例

```bash
# 显示当前配置
hermes config show

# 设置默认模型
hermes config set default_model=gpt-4o

# 设置温度参数
hermes config set temperature=0.8

# 获取特定配置
hermes config get default_model

# 编辑配置文件
hermes config edit
```

---

## hermes doctor

诊断工具，检查安装和配置是否正确。

```bash
hermes doctor [选项]
```

### 检查项目

- Python 版本兼容性
- 配置文件完整性
- API 密钥有效性
- 网络连接性
- 依赖库完整性
- 磁盘空间

### 示例

```bash
# 运行全面诊断
hermes doctor

# 检查特定项
hermes doctor --check network
hermes doctor --check api-keys
```

---

## hermes model

管理 AI 模型。

```bash
hermes model <子命令> [选项]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| `list` | 列出所有可用模型 |
| `switch <model>` | 切换默认模型 |
| `test <model>` | 测试模型连接 |
| `info <model>` | 查看模型详细信息 |

### 示例

```bash
# 列出可用模型
hermes model list

# 切换默认模型
hermes model switch claude-sonnet-4-20250514

# 测试模型
hermes model test gpt-4o
```

---

## hermes skills

管理技能。

```bash
hermes skills <子命令> [选项]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| `list` | 列出已安装的技能 |
| `install <name>` | 安装技能 |
| `remove <name>` | 移除技能 |
| `create <name>` | 创建新技能 |
| `info <name>` | 查看技能详情 |

### 示例

```bash
# 列出技能
hermes skills list

# 安装技能
hermes skills install web-search

# 创建新技能
hermes skills create my-custom-skill
```

---

## hermes cron

管理定时任务。

```bash
hermes cron <子命令> [选项]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| `list` | 列出所有定时任务 |
| `add` | 添加定时任务（交互式） |
| `remove <id>` | 移除定时任务 |
| `pause <id>` | 暂停定时任务 |
| `resume <id>` | 恢复定时任务 |

### 示例

```bash
hermes cron list
hermes cron add
```

---

## hermes memory

管理记忆系统。

```bash
hermes memory <子命令> [选项]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| `show` | 显示当前记忆内容 |
| `clear` | 清除记忆 |
| `export` | 导出记忆数据 |
| `import <file>` | 导入记忆数据 |

---

## hermes gateway

管理消息网关。

```bash
hermes gateway <子命令> [选项]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| `list` | 列出已配置的网关 |
| `start <name>` | 启动网关 |
| `stop <name>` | 停止网关 |
| `status <name>` | 查看网关状态 |

---

## hermes version

显示版本信息。

```bash
hermes version
```

---

## 退出码

| 退出码 | 说明 |
|--------|------|
| `0` | 成功 |
| `1` | 一般错误 |
| `2` | 配置错误 |
| `3` | API 错误 |
| `4` | 网络错误 |
| `5` | 权限错误 |
