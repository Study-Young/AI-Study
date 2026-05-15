# MCP 集成 (Model Context Protocol)

MCP（Model Context Protocol）是 Anthropic 推出的开放协议，允许 Claude Code 连接外部工具、数据源和服务。通过 MCP，Claude Code 可以超越文件系统限制，与您的开发工具链深度集成。

## 什么是 MCP？

MCP 是一种标准化的通信协议，定义了 AI 模型如何与外部工具和资源进行交互。

```
┌─────────────────┐         MCP 协议          ┌──────────────────┐
│                 │  ◄──── 工具调用/结果  ────  │                  │
│   Claude Code   │                            │   MCP 服务器     │
│                 │  ──── 资源读取/更新  ────►  │   (MCP Server)   │
└─────────────────┘                            └──────────────────┘
                                                       │
                                              ┌────────┴────────┐
                                              │                  │
                                         ┌────┴────┐   ┌───────┴────┐
                                         │  数据库  │   │  外部 API   │
                                         └─────────┘   └────────────┘
```

## MCP 服务器配置

MCP 服务器通过 JSON 配置文件定义。您可以在运行时通过 `--mcp-config` 参数指定。

### 配置文件结构

```json
{
  "mcpServers": {
    "<server-name>": {
      "command": "<可执行命令>",
      "args": ["<参数>"],
      "env": {
        "<环境变量>": "<值>"
      }
    }
  }
}
```

### 基本示例

创建 `mcp-config.json`：

```json
{
  "mcpServers": {
    "database": {
      "command": "node",
      "args": ["path/to/db-mcp-server.js"],
      "env": {
        "DB_HOST": "localhost",
        "DB_PORT": "5432",
        "DB_NAME": "myapp"
      }
    }
  }
}
```

### 使用 MCP 配置

```bash
claude --mcp-config ./mcp-config.json
# 或
claude --mcp-config /absolute/path/to/mcp-config.json
```

## MCP 作用域 (Scopes)

MCP 作用域定义了 MCP 服务器可以访问和操作的资源范围。合理配置作用域可以提高安全性。

### 作用域类型

| 作用域 | 说明 | 示例 |
|--------|------|------|
| `resources` | 读取资源 | 文件、数据库记录、API 响应 |
| `tools` | 调用工具 | 执行查询、发送通知、触发部署 |
| `prompts` | 使用提示模板 | 调用预定义的 MCP 提示模板 |

### 作用域配置示例

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["@anthropic-ai/mcp-github"],
      "scopes": {
        "resources": ["repos/*/issues", "repos/*/pulls"],
        "tools": ["create_issue", "list_pulls", "get_file"]
      }
    }
  }
}
```

## 内置 MCP 服务器

### GitHub MCP 服务器

与 GitHub API 集成，管理 Issue、PR、代码审查等。

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxxxxxxxxxxxxxxxxxxx"
      }
    }
  }
}
```

使用示例：

```
❯ 列出我的仓库中所有打开的 Issue
❯ 创建一个新的 Issue，标题为"优化登录页面性能"
❯ 审查 PR #42 的变更
```

### 数据库 MCP 服务器

连接 PostgreSQL、MySQL 等数据库。

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-postgres"],
      "env": {
        "PGHOST": "localhost",
        "PGPORT": "5432",
        "PGUSER": "admin",
        "PGPASSWORD": "password",
        "PGDATABASE": "myapp"
      }
    }
  }
}
```

使用示例：

```
❯ 查询 users 表中最近注册的 10 个用户
❯ 解释 orders 表的表结构并分析索引
❯ 查找过去 7 天最活跃的用户
```

### 文件系统 MCP 服务器

扩展文件操作能力。

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-filesystem"],
      "env": {
        "ALLOWED_PATHS": "/home/user/projects,/shared/data"
      }
    }
  }
}
```

## 自定义 MCP 服务器

您可以创建自己的 MCP 服务器，集成任意服务或 API。

### 简单 MCP 服务器示例 (Node.js)

创建 `weather-mcp-server.js`：

```javascript
#!/usr/bin/env node
/**
 * 天气查询 MCP 服务器示例
 */
import { Server } from '@anthropic-ai/mcp';

const server = new Server({
  name: 'weather',
  version: '1.0.0',
});

// 注册工具
server.tool(
  'get_weather',
  {
    city: { type: 'string', description: '城市名称' },
  },
  async ({ city }) => {
    // 模拟天气查询 API
    const weatherData = {
      '北京': { temperature: 22, condition: '晴', humidity: 45 },
      '上海': { temperature: 26, condition: '多云', humidity: 60 },
      '深圳': { temperature: 30, condition: '阴', humidity: 75 },
    };

    const data = weatherData[city] || {
      temperature: 20,
      condition: '未知',
      humidity: 50,
    };

    return {
      content: [
        {
          type: 'text',
          text: `${city}天气：${data.condition}，温度${data.temperature}°C，湿度${data.humidity}%`,
        },
      ],
    };
  }
);

// 注册资源
server.resource(
  'cities',
  'weather://cities',
  async (uri) => ({
    contents: [
      {
        uri: uri.href,
        text: JSON.stringify(['北京', '上海', '深圳', '广州', '杭州']),
      },
    ],
  })
);

server.run();
```

### 配置自定义 MCP 服务器

```json
{
  "mcpServers": {
    "weather": {
      "command": "node",
      "args": ["/path/to/weather-mcp-server.js"]
    }
  }
}
```

## 多个 MCP 服务器组合

您可以同时配置多个 MCP 服务器，Claude Code 会自动根据任务选择合适的服务器。

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxx"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-postgres"],
      "env": {
        "PGHOST": "localhost",
        "PGDATABASE": "myapp"
      }
    },
    "jira": {
      "command": "node",
      "args": ["mcp-servers/jira-server.js"],
      "env": {
        "JIRA_URL": "https://mycompany.atlassian.net",
        "JIRA_TOKEN": "xxx"
      }
    }
  }
}
```

使用示例：

```
❯ 查询数据库中所有未完成的任务，然后在 GitHub 上创建对应的 Issue
❯ 查看 Jira 中分配给当前用户的任务，并在本地创建对应的 TODO 注释
```

## 实际应用场景

### 场景一：数据库驱动的代码生成

```bash
claude --mcp-config ./mcp-db-config.json
```

```
❯ 查询数据库中的 orders 表结构，然后自动生成对应的 TypeScript 类型定义和 CRUD API 路由
```

### 场景二：自动化部署

```bash
claude --mcp-config ./mcp-deploy-config.json
```

```
❯ 查看当前代码中的版本号，然后更新 CHANGELOG.md，
   创建 Git tag，最后通过 MCP 触发 CI/CD 部署
```

### 场景三：全栈开发工作流

```json
{
  "mcpServers": {
    "postgres": { "command": "npx", "args": ["-y", "@anthropic-ai/mcp-postgres"] },
    "redis": { "command": "node", "args": ["mcp-servers/redis.js"] },
    "github": { "command": "npx", "args": ["-y", "@anthropic-ai/mcp-github"] },
    "slack": { "command": "node", "args": ["mcp-servers/slack.js"] }
  }
}
```

```
❯ 检查 Redis 缓存命中率，优化慢查询，提交 PR 并在 Slack 通知团队
```

## MCP 安全最佳实践

### 1. 最小权限环境变量

只为 MCP 服务器提供完成任务所需的最小环境变量。

### 2. 限制作用域

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/mcp-github"],
      "scopes": {
        "tools": ["create_issue", "list_pulls"],
        "resources": ["repos/*/issues"]
      }
    }
  }
}
```

### 3. 敏感信息管理

不要在配置文件中硬编码敏感信息，使用环境变量或密钥管理服务：

```json
{
  "mcpServers": {
    "database": {
      "command": "node",
      "args": ["mcp-server.js"],
      "env": {
        "DB_PASSWORD": "${DB_PASSWORD}"  // 从环境变量读取
      }
    }
  }
}
```

### 4. 定期审查 MCP 配置

确保所有 MCP 服务器的权限和访问范围符合当前的安全策略。

## 故障排除

### MCP 服务器连接失败

```bash
# 检查 MCP 服务器是否可执行
which npx  # 或对应的命令

# 测试 MCP 服务器是否正常启动
node mcp-server.js --test

# 查看 Claude Code 的详细日志
claude -p "测试 MCP 连接" --verbose
```

### 权限不足

确保 MCP 服务器具有访问所需资源的权限：

```bash
# 检查数据库连接
psql -h localhost -d myapp -c "SELECT 1"

# 检查 GitHub Token 权限
curl -H "Authorization: token ghp_xxx" https://api.github.com/user
```
