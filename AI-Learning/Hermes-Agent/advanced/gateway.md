# 消息网关

消息网关（Gateway）是 Hermes Agent 的统一平台集成层，负责管理所有即时通讯平台的连接、消息路由和事件处理。

## 架构概览

```
                    ┌─────────────────────────┐
                    │     Hermes Agent         │
                    │     (核心引擎)            │
                    └────────────┬────────────┘
                                 │
                    ┌────────────┴────────────┐
                    │      消息网关层           │
                    │    (Gateway Layer)       │
                    └──┬────┬────┬────┬───────┘
                       │    │    │    │
              ┌────────┘    │    │    └────────┐
              ▼             ▼    ▼             ▼
        ┌──────────┐ ┌────────┐ ┌────────┐ ┌────────┐
        │ Telegram │ │ Discord│ │  Slack │ │WhatsApp│
        │ Gateway  │ │ Gateway│ │ Gateway│ │ Gateway│
        └──────────┘ └────────┘ └────────┘ └────────┘
              │             │        │            │
              ▼             ▼        ▼            ▼
        ┌──────────┐ ┌────────┐ ┌────────┐ ┌────────┐
        │Telegram  │ │Discord │ │Slack   │ │WhatsApp│
        │  API     │ │  API   │ │  API   │ │  API   │
        └──────────┘ └────────┘ └────────┘ └────────┘
```

## 核心功能

- **统一消息接口**：所有平台统一的消息格式和事件模型
- **自动重连**：断线自动重连，保证服务可用性
- **速率限制**：平台特定的 API 调用频率控制
- **消息队列**：异步消息处理，防止阻塞
- **会话管理**：每个平台独立维护对话上下文
- **平台适配**：处理各平台特有的消息格式和限制

## 配置网关

### 启用/禁用

```yaml
# ~/.hermes/config.yaml
gateway:
  enabled: true                    # 全局开关
  
  # 平台特定配置
  platforms:
    telegram:
      enabled: true
      # ... 平台特定配置
    discord:
      enabled: false
    slack:
      enabled: false
```

### 平台配置详情

每个平台的配置包括：

```yaml
platforms:
  telegram:
    enabled: true
    name: "hermes_bot"                          # Bot 名称
    token: "${TELEGRAM_BOT_TOKEN}"               # Bot Token
    allowed_users: []                            # 允许的用户 ID（空=全部允许）
    allowed_groups: []                           # 允许的群组 ID
    admin_users: []                              # 管理员用户 ID
    max_message_length: 4096                     # 消息最大长度
    
  discord:
    enabled: true
    name: "Hermes Agent"
    token: "${DISCORD_BOT_TOKEN}"
    guild_id: "your-guild-id"
    allowed_channels: []                         # 允许的频道
    admin_roles: []                              # 管理员角色
    command_prefix: "/"                          # 命令前缀
    
  slack:
    enabled: true
    name: "Hermes Agent"
    token: "${SLACK_BOT_TOKEN}"
    signing_secret: "${SLACK_SIGNING_SECRET}"
    allowed_channels: []                         # 允许的频道
    bot_mention_only: true                       # 仅 @提及 时响应
```

## 启动网关

### CLI 命令

```bash
# 启动指定网关
hermes gateway start telegram

# 停止指定网关
hermes gateway stop telegram

# 重启指定网关
hermes gateway restart telegram

# 查看网关状态
hermes gateway status telegram

# 列出所有已配置的网关
hermes gateway list

# 查看网关日志
hermes gateway logs telegram
```

### 自动启动

配置自动启动的网关：

```yaml
# config.yaml
gateway:
  auto_start:
    - telegram
    - discord
```

## 消息路由

### 消息处理流程

```
用户发送消息
    │
    ▼
平台 API → Gateway Adapter → 消息标准化
    │
    ▼
消息队列（异步处理）
    │
    ▼
权限检查
    │
    ▼
会话路由（按用户/群组分配会话）
    │
    ▼
Hermes Agent 核心处理
    │
    ▼
回复生成 → 格式适配 → 平台 API → 用户接收
```

### 消息格式转换

网关自动处理各平台间的消息格式差异：

| 特性 | Telegram | Discord | Slack | WhatsApp |
|------|----------|---------|-------|----------|
| Markdown | ✅ 部分 | ✅ 全部 | ✅ 部分 | ❌ |
| 富文本 | ❌ | ✅ | ✅ | ❌ |
| 图片 | ✅ | ✅ | ✅ | ✅ |
| 文件 | ✅ | ✅ | ✅ | ✅ |
| 语音 | ✅ | ✅ | ❌ | ✅ |
| 按钮/交互 | ✅ | ✅ | ✅ | ❌ |
| 表情回应 | ✅ | ✅ | ✅ | ❌ |

## 会话管理

### 独立会话

每个平台上的每个用户/群组都有独立的会话上下文：

```yaml
gateway:
  session:
    isolation: true                    # 跨平台会话隔离
    max_history: 50                    # 每个会话最大历史消息数
    timeout: 3600                      # 会话超时时间（秒）
    strategy: "last_user"              # 会话切换策略
```

### 跨平台会话（可选）

如果需要跨平台共享会话上下文：

```yaml
gateway:
  session:
    isolation: false                   # 关闭隔离
    merge_users:                       # 合并用户
      - platforms: [telegram, discord]
        user_id: "user-123"
```

## 事件处理

网关可以处理各种平台事件，不仅仅是消息：

| 事件类型 | Telegram | Discord | Slack |
|----------|----------|---------|-------|
| 消息 | ✅ | ✅ | ✅ |
| 编辑消息 | ✅ | ✅ | ✅ |
| 删除消息 | ❌ | ✅ | ✅ |
| 成员加入 | ✅ | ✅ | ✅ |
| 成员离开 | ❌ | ✅ | ✅ |
| 反应/表情 | ✅ | ✅ | ✅ |
| 命令 | ✅ | ✅ | ✅ |
| 文件上传 | ✅ | ✅ | ✅ |
| 投票 | ❌ | ✅ | ✅ |

## 错误处理和重试

```yaml
gateway:
  retry:
    max_attempts: 5                    # 最大重试次数
    initial_delay: 1                   # 初始延迟（秒）
    max_delay: 60                      # 最大延迟（秒）
    backoff: exponential               # 退避策略：exponential | linear | fixed
    
  error_handling:
    notify_admin: true                 # 错误通知管理员
    fallback_response: "暂时无法处理，请稍后再试。"
    auto_restart: true                 # 自动重启断开的网关
```

## 健康监控

```bash
# 查看所有网关状态
hermes gateway status

# 输出示例：
# Telegram Gateway  : ✅ 运行中 (连接数: 3, 消息/分钟: 12)
# Discord Gateway   : ✅ 运行中 (连接数: 5, 消息/分钟: 8)
# Slack Gateway     : ❌ 已断开 (最后活动: 2分钟前)
```

## 最佳实践

1. **逐个启用**：先启用一个平台测试通过后，再添加其他平台
2. **设置白名单**：使用 `allowed_users` 和 `allowed_channels` 限制访问范围
3. **使用环境变量**：所有令牌和密钥通过 `.env` 文件管理
4. **监控日志**：定期检查网关日志，及时发现连接问题
5. **速率控制**：配置速率限制，避免触发 API 限频
6. **测试机器人**：使用测试 Bot 进行功能验证
7. **分离配置**：不同环境（开发/生产）使用不同配置
