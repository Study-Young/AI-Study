# 定时任务（Cron）

Hermes Agent 内置了强大的定时任务系统，允许 AI 在指定的时间自动执行任务。无论是每日报告、定时提醒还是周期性数据检查，都能轻松实现。

## 功能概述

- **一次性任务**：在指定时间执行一次
- **周期性任务**：按 Cron 表达式周期执行
- **条件触发**：在满足特定条件时执行
- **任务链**：一个任务完成后自动触发下一个
- **失败重试**：自动重试失败的任务
- **通知推送**：任务完成后发送通知

## Cron 表达式

Hermes Agent 使用标准的 5 字段 Cron 表达式：

```
┌───────── 分钟 (0 - 59)
│ ┌───────── 小时 (0 - 23)
│ │ ┌───────── 日 (1 - 31)
│ │ │ ┌───────── 月 (1 - 12)
│ │ │ │ ┌───────── 星期 (0 - 7，0 和 7 都表示星期日)
│ │ │ │ │
* * * * *
```

### 常用示例

| 表达式 | 说明 |
|--------|------|
| `0 9 * * *` | 每天早上 9:00 |
| `0 0 * * *` | 每天午夜 |
| `*/15 * * * *` | 每 15 分钟 |
| `0 9 * * 1-5` | 工作日每天早上 9:00 |
| `0 0 1 * *` | 每月 1 日午夜 |
| `0 0 * * 0` | 每周日午夜 |
| `0 */2 * * *` | 每 2 小时 |

### 特殊字符串

| 字符串 | 等同于 |
|--------|--------|
| `@yearly` | `0 0 1 1 *` |
| `@monthly` | `0 0 1 * *` |
| `@weekly` | `0 0 * * 0` |
| `@daily` | `0 0 * * *` |
| `@hourly` | `0 * * * *` |

## 创建定时任务

### 通过 CLI 创建

```bash
hermes cron add
```

这会启动一个交互式向导：

```
? 任务名称: daily-report
? 任务描述: 生成每日工作报告
? Cron 表达式: 0 9 * * 1-5
? 任务类型: 周期性
? 提示词: 生成今天的 AI 行业新闻摘要
? 是否启用: 是
```

### 通过配置文件创建

```yaml
# ~/.hermes/config.yaml
cron:
  jobs:
    - name: daily-report
      description: "生成每日工作报告"
      schedule: "0 9 * * 1-5"
      type: recurring
      prompt: "生成今天的 AI 行业新闻摘要"
      enabled: true
      
    - name: weekly-summary
      description: "每周总结"
      schedule: "0 10 * * 1"
      type: recurring
      prompt: "总结上周的主要工作和进展"
      enabled: true
      
    - name: one-time-reminder
      description: "一次性提醒"
      schedule: "2026-05-20T15:00:00Z"
      type: one_shot
      prompt: "提醒我参加下午 3 点的会议"
      enabled: true
```

## 任务配置详解

```yaml
jobs:
  - name: my-task                # 任务名称（唯一标识）
    description: "我的任务描述"    # 简短描述
    schedule: "0 9 * * *"         # Cron 表达式或 ISO 时间
    type: recurring               # recurring（周期）| one_shot（一次性）
    prompt: "执行任务的提示词"     # 发送给 AI 的提示
    enabled: true                 # 是否启用
    
    # 可选配置
    model: gpt-4o                 # 指定使用的模型
    temperature: 0.5              # 温度参数
    max_tokens: 2048              # 最大 Token 数
    platform: terminal            # 输出平台
    notification:                 # 通知配置
      on_success: true            # 成功时通知
      on_failure: true            # 失败时通知
      channel: "telegram"         # 通知渠道
      recipient: "123456789"      # 接收者 ID
    retry:                        # 重试策略
      max_retries: 3              # 最大重试次数
      retry_delay: 300            # 重试间隔（秒）
    timeout: 300                  # 超时时间（秒）
    tags: ["report", "daily"]     # 标签
```

## 管理定时任务

### CLI 命令

```bash
# 列出所有任务
hermes cron list

# 添加新任务
hermes cron add

# 移除任务
hermes cron remove daily-report

# 暂停任务
hermes cron pause daily-report

# 恢复任务
hermes cron resume daily-report

# 立即执行任务
hermes cron run daily-report

# 查看任务日志
hermes cron logs daily-report
```

### 聊天命令

```text
/cron                           # 列出所有定时任务
/cron add                       # 交互式添加任务
/cron remove <name>             # 移除任务
/cron pause <name>              # 暂停任务
/cron resume <name>             # 恢复任务
/cron run <name>                # 立即执行任务
/cron logs <name>               # 查看任务日志
```

## 任务输出

定时任务执行的结果会保存到日志中，并通过配置的通知渠道发送。

### 输出存储

```
~/.hermes/cron/logs/
├── daily-report/
│   ├── 2026-05-15T09-00-00.md
│   ├── 2026-05-14T09-00-00.md
│   └── 2026-05-13T09-00-00.md
└── weekly-summary/
    └── 2026-05-11T10-00-00.md
```

### 输出格式示例

```markdown
# 定时任务执行报告

**任务**: daily-report
**执行时间**: 2026-05-15 09:00:00 UTC
**状态**: ✅ 成功
**耗时**: 12.3 秒
**Token 使用**: 输入 450 / 输出 820

## 执行结果

今日 AI 行业新闻摘要：
1. ...（内容）
2. ...（内容）
3. ...（内容）

## 附件

- 完整报告：report-2026-05-15.md
```

## 高级用法

### 任务链

将一个任务的输出作为下一个任务的输入：

```yaml
cron:
  jobs:
    - name: collect-data
      schedule: "0 8 * * *"
      prompt: "收集今天的数据"
      
    - name: analyze-data
      schedule:
        after: collect-data      # 在 collect-data 完成后执行
      prompt: "分析收集到的数据"
      
    - name: send-report
      schedule:
        after: analyze-data
      prompt: "生成并发送分析报告"
```

### 条件任务

只在满足特定条件时执行：

```yaml
jobs:
  - name: check-server
    schedule: "*/5 * * * *"
    prompt: "检查服务器状态"
    condition: "仅当上次检查返回异常时才通知"
```

### 动态调度

任务可以调用 AI 来决定下一次执行时间：

```yaml
jobs:
  - name: smart-reminder
    schedule: "dynamic"
    prompt: "根据我当前的工作进度，确定下次提醒的最佳时间"
```

## 最佳实践

1. **合理设置间隔**：避免过于频繁的短间隔任务
2. **配置重试策略**：为重要任务设置重试机制
3. **监控任务日志**：定期检查任务执行状态
4. **使用通知**：配置失败通知，及时发现问题
5. **任务命名规范**：使用有意义的名称便于管理
6. **测试任务**：先用 `hermes cron run` 测试再启用
7. **资源管理**：注意 Token 消耗和 API 调用频率
