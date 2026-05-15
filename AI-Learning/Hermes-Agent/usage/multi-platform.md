# 多平台部署

Hermes Agent 支持在多个即时通讯平台上运行，通过消息网关（Gateway）实现统一的 AI 代理体验。

## 支持的平台

| 平台 | 状态 | 功能支持 |
|------|------|----------|
| 终端 CLI | ✅ 原生 | 全部功能 |
| Telegram | ✅ 完整支持 | 聊天、命令、文件、语音 |
| Discord | ✅ 完整支持 | 聊天、命令、文件、Slash 命令 |
| Slack | ✅ 完整支持 | 聊天、命令、文件 |
| WhatsApp | ✅ 支持 | 聊天、命令 |
| Signal | ⚠️ Beta | 基本聊天 |
| Matrix | ⚠️ Beta | 基本聊天 |
| Web UI | 🔄 开发中 | 即将推出 |

---

## 通用配置

所有平台的核心配置在 `~/.hermes/config.yaml` 中：

```yaml
# 全局设置
default_model: gpt-4o
temperature: 0.7
max_tokens: 4096

# 平台特定设置
platforms:
  telegram:
    enabled: true
    token: "${TELEGRAM_BOT_TOKEN}"
    allowed_users: []  # 留空表示允许所有用户
  discord:
    enabled: false
    token: "${DISCORD_BOT_TOKEN}"
    guild_id: "your-guild-id"
  slack:
    enabled: false
    token: "${SLACK_BOT_TOKEN}"
    signing_secret: "${SLACK_SIGNING_SECRET}"
```

API 密钥和令牌存储在 `~/.hermes/.env` 中：

```env
TELEGRAM_BOT_TOKEN=123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
DISCORD_BOT_TOKEN=your-discord-bot-token
SLACK_BOT_TOKEN=xoxb-your-slack-token
SLACK_SIGNING_SECRET=your-slack-signing-secret
WHATSAPP_ACCESS_TOKEN=your-whatsapp-token
```

---

## Telegram 部署

### 1. 创建 Bot

1. 在 Telegram 中搜索 **@BotFather**
2. 发送 `/newbot` 命令
3. 按照提示设置 bot 名称和用户名
4. BotFather 会提供一个 HTTP API Token

### 2. 配置

在 `.env` 中添加：

```env
TELEGRAM_BOT_TOKEN=123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11
```

在 `config.yaml` 中启用 Telegram：

```yaml
platforms:
  telegram:
    enabled: true
    token: "${TELEGRAM_BOT_TOKEN}"
    allowed_users:
      - 123456789  # 你的 Telegram 用户 ID（可选）
```

### 3. 启动

```bash
hermes chat --platform telegram
```

或通过网关启动：

```bash
hermes gateway start telegram
```

### 4. 使用

在 Telegram 中：
- **私聊**：直接向 bot 发送消息
- **群组**：将 bot 添加到群组，使用 `@bot用户名` 提及
- **斜杠命令**：支持所有 Hermes Agent 斜杠命令

---

## Discord 部署

### 1. 创建 Bot

1. 访问 [Discord Developer Portal](https://discord.com/developers/applications)
2. 点击 **New Application**，输入名称
3. 进入 **Bot** 页面，点击 **Add Bot**
4. 点击 **Reset Token** 获取 token
5. 在 **Bot** 设置中启用：
   - **Message Content Intent**
   - **Server Members Intent**（可选）

### 2. 邀请 Bot 到服务器

在 **OAuth2 > URL Generator** 中：
1. 选择 **bot** Scopes
2. 选择权限：
   - Send Messages
   - Read Message History
   - Use Slash Commands
   - Attach Files（可选）
3. 复制生成的 URL，在浏览器中打开邀请

### 3. 配置

```env
DISCORD_BOT_TOKEN=your-discord-bot-token
```

```yaml
platforms:
  discord:
    enabled: true
    token: "${DISCORD_BOT_TOKEN}"
    guild_id: "your-guild-id"
    allowed_channels: []  # 限制频道（可选）
```

---

## Slack 部署

### 1. 创建 Slack App

1. 访问 [Slack API](https://api.slack.com/apps)
2. 点击 **Create New App > From Scratch**
3. 填写应用名称和工作区

### 2. 配置权限

在 **OAuth & Permissions** 中添加 Bot Token Scopes：
- `chat:write`
- `channels:history`
- `groups:history`
- `im:history`
- `mpim:history`
- `reactions:read`

### 3. 启用 Events

在 **Event Subscriptions** 中：
1. 启用 Events
2. 设置 Request URL：`https://your-server/events`
3. 订阅事件：`message.channels`、`message.im`、`message.groups`

### 4. 安装 App

在 **Install App** 页面点击 **Install to Workspace**，获取 Bot Token。

### 5. 配置

```env
SLACK_BOT_TOKEN=xoxb-your-slack-token
SLACK_SIGNING_SECRET=your-signing-secret
```

```yaml
platforms:
  slack:
    enabled: true
    token: "${SLACK_BOT_TOKEN}"
    signing_secret: "${SLACK_SIGNING_SECRET}"
```

---

## WhatsApp 部署

### 1. 设置 WhatsApp Business API

1. 访问 [Meta for Developers](https://developers.facebook.com/)
2. 创建 WhatsApp Business App
3. 配置 Webhook
4. 获取 Access Token

### 2. 配置

```env
WHATSAPP_ACCESS_TOKEN=your-whatsapp-token
WHATSAPP_PHONE_NUMBER_ID=your-phone-number-id
WHATSAPP_WEBHOOK_VERIFY_TOKEN=your-verify-token
```

```yaml
platforms:
  whatsapp:
    enabled: true
    access_token: "${WHATSAPP_ACCESS_TOKEN}"
    phone_number_id: "${WHATSAPP_PHONE_NUMBER_ID}"
    webhook_verify_token: "${WHATSAPP_WEBHOOK_VERIFY_TOKEN}"
```

---

## 多平台同时运行

可以同时在多个平台上运行 Hermes Agent：

```bash
# 在终端中聊天，同时在 Telegram 和 Discord 上运行
hermes chat --platform terminal &
hermes gateway start telegram &
hermes gateway start discord &
```

所有平台共享同一个 AI 代理实例，但每个平台维护独立的对话历史。

## 平台特定的斜杠命令

某些斜杠命令在不同平台上略有差异：

| 命令 | Telegram | Discord | Slack |
|------|----------|---------|-------|
| `/help` | ✅ | ✅ | ✅ |
| `/new` | ✅ | ✅ | ✅ |
| `/retry` | ✅ | ✅ | ❌ |
| `/model` | ✅ | ✅ | ✅ |
| `/skills` | ✅ | ✅ | ✅ |
| `/cron` | ✅ | ❌ | ❌ |
| `/voice` | ✅ | ❌ | ❌ |

## 安全建议

- **使用白名单**：通过 `allowed_users` 或 `allowed_channels` 限制访问
- **隔离环境**：为不同平台使用不同的配置
- **速率限制**：配置 API 调用频率限制
- **日志监控**：启用日志记录以跟踪使用情况
- **密钥轮换**：定期更换 API 密钥和 token
