# 斜杠命令

Hermes Agent 在聊天界面中提供斜杠命令（Slash Commands），用于控制会话、管理配置和调用功能。所有命令以 `/` 开头。

## 基本命令

### /new

开始一个新的对话会话。

```text
/new
```

作用：
- 清除当前对话历史
- 重置上下文窗口
- 保留系统提示词和技能配置

### /retry

重新生成 AI 的上一条回复。

```text
/retry
```

当对 AI 回复不满意时，使用此命令让 AI 重新生成。可配合 `/feedback` 提供改进方向。

### /exit 或 /quit

退出当前聊天会话。

```text
/exit
```

### /help

显示所有可用命令的帮助信息。

```text
/help
```

---

## 模型命令

### /model

切换或查看当前使用的模型。

```text
/model                          # 查看当前模型
/model claude-sonnet-4-20250514  # 切换到指定模型
/model list                     # 列出可用模型
```

### /temperature

设置生成温度参数。

```text
/temperature 0.7                # 设置温度为 0.7
/temperature                    # 查看当前温度
```

### /max-tokens

设置最大生成长度。

```text
/max-tokens 4096                # 设置最大 4096 tokens
```

---

## 对话命令

### /feedback

提供反馈意见，帮助改进回复质量。

```text
/feedback 这个回答太长了，请简洁一些
```

### /summarize

总结当前对话的核心内容。

```text
/summarize
```

### /save

保存当前对话到文件。

```text
/save my-conversation.md        # 保存到指定文件
/save                           # 保存到默认文件
```

### /load

加载之前保存的对话。

```text
/load my-conversation.md
```

### /export

以 JSON 格式导出完整的对话历史。

```text
/export chat-history.json
```

---

## 技能与工具命令

### /skills

管理已加载的技能。

```text
/skills                         # 列出已加载的技能
/skills enable web-search       # 启用指定技能
/skills disable web-search      # 禁用指定技能
/skills info web-search         # 查看技能详情
```

### /tools

查看和管理可用工具。

```text
/tools                          # 列出所有可用工具
/tools info <name>              # 查看工具详情
```

### /functions

列出 AI 可以调用的所有函数。

```text
/functions
```

---

## 记忆命令

### /memory

查看和管理记忆。

```text
/memory                         # 显示当前记忆内容
/memory clear                   # 清除记忆
/memory search <关键词>          # 搜索记忆
/memory add <内容>              # 手动添加记忆
```

### /remember

让 AI 主动记住当前对话中的重要信息。

```text
/remember
```

---

## 定时任务命令

### /cron

管理定时任务。

```text
/cron                           # 列出所有定时任务
/cron add                       # 添加定时任务（交互式）
/cron remove <id>               # 移除定时任务
/cron pause <id>                # 暂停定时任务
/cron resume <id>               # 恢复定时任务
/cron run <id>                  # 立即执行定时任务
```

---

## 系统命令

### /status

查看当前会话的系统状态。

```text
/status
```

显示信息包括：
- 当前模型
- 当前平台
- 已加载技能
- Token 使用统计
- 系统版本

### /config

查看或修改运行时配置。

```text
/config                         # 查看当前配置
/config set <key=value>         # 设置配置项
/config reset                   # 重置配置
```

### /platform

切换或查看当前平台。

```text
/platform                       # 查看当前平台
/platform telegram              # 切换到 Telegram 平台
/platform discord               # 切换到 Discord 平台
```

### /plugins

管理插件。

```text
/plugins                        # 列出已安装插件
/plugins install <name>         # 安装插件
/plugins remove <name>          # 移除插件
```

### /voice

切换语音模式（需先配置语音支持）。

```text
/voice                          # 切换语音输入/输出
/voice input                    # 启用语音输入
/voice output                   # 启用语音输出
/voice off                      # 关闭语音
```

---

## 调试命令

### /debug

进入调试模式，显示详细的技术信息。

```text
/debug
```

### /tokens

显示当前对话的 Token 使用量。

```text
/tokens
```

### /inspect

检查特定消息的详细信息。

```text
/inspect                        # 检查最后一条消息
/inspect <消息编号>              # 检查指定消息
```

---

## 自定义命令

用户可以在配置文件中定义自定义斜杠命令，例如：

```yaml
# ~/.hermes/config.yaml
commands:
  greet:
    description: "问候用户"
    action: "请用友好的语气问候我"
  code-review:
    description: "审查当前代码"
    action: "请审查最后一条消息中的代码，指出潜在问题"
```

定义后即可使用：

```text
/greet
/code-review
```

## 命令组合

某些命令可以组合使用，例如：

```text
/new /model claude-sonnet-4-20250514
```

这会同时开始新对话并切换模型。

## 命令快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+N` | 新对话（等同 `/new`） |
| `Ctrl+R` | 重试（等同 `/retry`） |
| `Ctrl+D` | 退出（等同 `/exit`） |
| `Ctrl+L` | 清屏 |
| `Ctrl+C` | 中断生成 |
