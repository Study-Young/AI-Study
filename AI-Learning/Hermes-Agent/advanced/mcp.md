# MCP 协议支持

Hermes Agent 原生支持 **Model Context Protocol（MCP）**，这是一个开放标准，用于在大语言模型应用和外部工具/数据源之间建立安全、标准化的连接。

## 什么是 MCP？

MCP（Model Context Protocol）由 Anthropic 提出，是一种类似 USB-C 的协议——提供标准化的接口，让 AI 应用可以连接各种工具和数据源。通过 MCP，Hermes Agent 可以：

- 查询数据库（PostgreSQL、MySQL、SQLite 等）
- 访问文件系统
- 调用外部 API
- 使用版本控制工具
- 连接浏览器自动化工具
- 运行代码解释器

## MCP 架构

```
┌─────────────────────────────────────────┐
│            Hermes Agent                  │
│           (MCP 客户端)                    │
└──────────────┬──────────────────────────┘
               │
    ┌──────────┴──────────┐
    │     MCP 协议         │
    │  (JSON-RPC 2.0)     │
    └──────────┬──────────┘
               │
    ┌──────────┴──────────┐
    │    MCP 服务器        │
    │ (提供工具和资源)      │
    └─────────────────────┘
               │
    ┌──────────┴──────────┐
    │   外部系统/API       │
    │ (数据库、文件、Web)   │
    └─────────────────────┘
```

## 配置 MCP 服务器

### 基本配置

在 `~/.hermes/config.yaml` 中配置 MCP 服务器：

```yaml
# ~/.hermes/config.yaml
mcp:
  enabled: true
  
  # MCP 服务器配置
  servers:
    # 通过命令启动的服务器
    filesystem:
      command: npx
      args:
        - -y
        - "@modelcontextprotocol/server-filesystem"
        - /home/user/projects
    
    # 通过 Python 启动的服务器
    database:
      command: python
      args:
        - -m
        - mcp_server_postgres
      env:
        PGHOST: localhost
        PGPORT: "5432"
        PGDATABASE: mydb
        PGUSER: user
        PGPASSWORD: "${DB_PASSWORD}"
    
    # Web 抓取服务器
    web-scraper:
      command: uvx
      args:
        - mcp-server-puppeteer
    
    # Git 操作服务器
    git:
      command: npx
      args:
        - -y
        - "@modelcontextprotocol/server-github"
      env:
        GITHUB_TOKEN: "${GITHUB_TOKEN}"
    
    # 自定义 MCP 服务器
    my-custom-server:
      command: python
      args:
        - /path/to/my-mcp-server.py
```

### 使用 STDIO 通信

MCP 服务器通过 STDIO（标准输入/输出）与 Hermes Agent 通信：

```
Hermes Agent  →  STDIN  →  MCP 服务器
Hermes Agent  ←  STDOUT ←  MCP 服务器
```

所有通信使用 JSON-RPC 2.0 协议。

## MCP 工具示例

### 文件系统工具

当配置了 `filesystem` MCP 服务器后，AI 可以：

```python
# 读取文件
read_file(path="/home/user/projects/main.py")

# 写入文件
write_file(path="/home/user/projects/notes.txt", content="笔记内容")

# 列出目录
list_directory(path="/home/user/projects")

# 搜索文件
search_files(pattern="*.py", path="/home/user/projects")

# 获取文件信息
get_file_info(path="/home/user/projects/main.py")
```

### 数据库工具

当配置了 `database` MCP 服务器后，AI 可以：

```python
# 执行 SQL 查询
execute_query(query="SELECT * FROM users LIMIT 10")

# 列出表
list_tables()

# 查看表结构
describe_table(table="users")

# 执行事务
execute_transaction(queries=[
    "INSERT INTO logs (action) VALUES ('test')",
    "UPDATE users SET status='active' WHERE id=1"
])
```

### Web 工具

当配置了 `web-scraper` MCP 服务器后，AI 可以：

```python
# 抓取网页
scrape_page(url="https://example.com")

# 截图
screenshot(url="https://example.com", output="/tmp/screenshot.png")

# 点击元素
click_element(selector="#button")

# 填写表单
fill_form(selector="#search", value="Hermes Agent")
```

### GitHub 工具

当配置了 `git` MCP 服务器后，AI 可以：

```python
# 创建 Issue
create_issue(repo="owner/repo", title="Bug report", body="描述")

# 搜索代码
search_code(query="function hermes", repo="owner/repo")

# 查看 PR
get_pull_request(repo="owner/repo", number=42)

# 创建文件
create_file(repo="owner/repo", path="docs/new.md", content="# New doc")
```

## 自定义 MCP 服务器

### Python 示例

创建一个简单的 MCP 服务器：

```python
# my-mcp-server.py
import json
import sys
from typing import Any

def handle_request(request: dict) -> dict:
    """处理 MCP 请求"""
    method = request.get("method")
    params = request.get("params", {})
    
    if method == "list_tools":
        return {
            "tools": [
                {
                    "name": "hello_world",
                    "description": "一个简单的问候工具",
                    "inputSchema": {
                        "type": "object",
                        "properties": {
                            "name": {
                                "type": "string",
                                "description": "要问候的人名"
                            }
                        },
                        "required": ["name"]
                    }
                }
            ]
        }
    
    elif method == "call_tool":
        if params["name"] == "hello_world":
            name = params["arguments"]["name"]
            return {
                "content": [
                    {
                        "type": "text",
                        "text": f"你好，{name}！欢迎使用自定义 MCP 服务器。"
                    }
                ]
            }
    
    return {"error": "未知方法"}

def main():
    """MCP 服务器主循环"""
    for line in sys.stdin:
        try:
            request = json.loads(line.strip())
            response = handle_request(request)
            print(json.dumps(response), flush=True)
        except Exception as e:
            print(json.dumps({"error": str(e)}), flush=True)

if __name__ == "__main__":
    main()
```

### Node.js 示例

```javascript
// my-mcp-server.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  { name: "my-custom-server", version: "1.0.0" },
  { capabilities: { tools: {} } }
);

server.setRequestHandler("tools/list", async () => ({
  tools: [{
    name: "greet",
    description: "发送问候",
    inputSchema: {
      type: "object",
      properties: {
        name: { type: "string" }
      }
    }
  }]
}));

server.setRequestHandler("tools/call", async (request) => {
  if (request.params.name === "greet") {
    return {
      content: [{ type: "text", text: `Hello ${request.params.arguments.name}!` }]
    };
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

## 可用 MCP 服务器列表

以下是一些常用的 MCP 服务器：

| 服务器 | 描述 | 安装命令 |
|--------|------|----------|
| `server-filesystem` | 文件系统操作 | `npx @modelcontextprotocol/server-filesystem` |
| `server-github` | GitHub API 集成 | `npx @modelcontextprotocol/server-github` |
| `server-postgres` | PostgreSQL 数据库 | `pip install mcp-server-postgres` |
| `server-sqlite` | SQLite 数据库 | `pip install mcp-server-sqlite` |
| `server-puppeteer` | 浏览器自动化 | `npx @modelcontextprotocol/server-puppeteer` |
| `server-sentry` | Sentry 错误监控 | `pip install mcp-server-sentry` |
| `server-slack` | Slack 集成 | `npx @modelcontextprotocol/server-slack` |
| `server-google-maps` | 谷歌地图 | `npx @modelcontextprotocol/server-google-maps` |

## 安全注意事项

1. **权限控制**：MCP 服务器可以访问文件系统和数据库，需谨慎配置
2. **环境变量隔离**：敏感信息通过环境变量传递，不暴露在日志中
3. **路径限制**：文件系统服务器应限制可访问的目录范围
4. **命令白名单**：只允许执行预定义的 MCP 服务器
5. **审计日志**：记录所有 MCP 工具调用
6. **超时控制**：设置合理的超时时间，防止长时间占用

## 故障排除

```bash
# 测试 MCP 服务器连接
hermes doctor --check mcp

# 查看 MCP 服务器状态
hermes mcp status

# 列出所有已配置的 MCP 服务器
hermes mcp list

# 测试特定 MCP 工具
hermes mcp call filesystem list_directory /home/user
```
