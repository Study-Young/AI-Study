# 打印模式（Print Mode）

打印模式（`-p` 或 `--print`）是 Claude Code 的单次执行模式，非常适合自动化脚本、CI/CD 流水线和一次性任务。

## 基本用法

```bash
# 基础用法
claude -p "解释什么是 RESTful API"

# 处理文件
claude -p "重构 src/utils.js，将重复代码提取为函数"

# 结合管道
cat data.json | claude -p "分析这个 JSON 数据的结构并生成 TypeScript 类型定义"
```

打印模式的特点：

- **单次执行**：处理完请求后自动退出
- **无状态**：每次调用都是独立的
- **直接输出**：结果直接打印到标准输出
- **可脚本化**：完美适合 shell 脚本和自动化

## 结构化 JSON 输出

使用 `--output-format json` 参数获取结构化输出：

```bash
claude -p "分析这个代码中的安全漏洞" --output-format json
```

JSON 输出格式：

```json
{
  "text": "分析结果文本...",
  "turns": 3,
  "tool_uses": [
    {
      "tool": "Read",
      "input": {"file_path": "src/app.js"},
      "output": "文件内容..."
    },
    {
      "tool": "Edit",
      "input": {"file_path": "src/app.js", "old_string": "...", "new_string": "..."},
      "output": "编辑成功"
    }
  ],
  "tokens": {
    "input": 1250,
    "output": 800,
    "total": 2050
  }
}
```

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "text": {
      "type": "string",
      "description": "Claude Code 的文本响应内容"
    },
    "turns": {
      "type": "integer",
      "description": "执行过程中使用的对话轮数"
    },
    "tool_uses": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "tool": {
            "type": "string",
            "description": "使用的工具名称（Read, Edit, Write, Bash 等）"
          },
          "input": {
            "type": "object",
            "description": "工具的输入参数"
          },
          "output": {
            "type": "string",
            "description": "工具的执行结果"
          }
        }
      }
    },
    "tokens": {
      "type": "object",
      "properties": {
        "input": {
          "type": "integer",
          "description": "输入 token 数量"
        },
        "output": {
          "type": "integer",
          "description": "输出 token 数量"
        },
        "total": {
          "type": "integer",
          "description": "总 token 数量"
        }
      }
    },
    "error": {
      "type": "string",
      "description": "如果执行出错，包含错误信息"
    }
  }
}
```

## 流式输出

实时查看 Claude Code 的思考过程：

```bash
# 默认启用流式输出
claude -p "编写一个冒泡排序算法的 Python 实现"

# 禁用流式输出
claude -p "解释微服务架构" --no-stream
```

流式输出示例：

```
[分析请求...]
[读取项目文件 src/sort.py...]
[分析现有代码...]
┌─ 正在实现冒泡排序算法
│  def bubble_sort(arr):
│      n = len(arr)
│      for i in range(n):
│          ...
└─ 完成！
```

## 管道输入（Piped Input）

### 从文件管道输入

```bash
# 将文件内容传给 Claude
cat package.json | claude -p "检查这个 package.json 的依赖是否有已知的安全问题"

# 链式处理
curl -s https://api.github.com/repos/user/repo | claude -p "分析这个 GitHub 仓库的统计数据"
```

### 从命令输出管道输入

```bash
# 分析命令输出
git log --oneline -20 | claude -p "总结最近的 git 提交记录"

# 分析构建错误
npm run build 2>&1 | claude -p "分析构建错误并给出修复建议"

# 分析测试结果
npm test 2>&1 | claude -p "分析测试失败原因"
```

### Here 文档

```bash
# 多行输入
claude -p "将这个 YAML 配置转换为 Docker Compose 配置" << 'EOF'
app:
  name: myapp
  port: 3000
  database:
    host: localhost
    port: 5432
    name: myapp_db
EOF
```

## 实用场景示例

### CI/CD 自动化

```yaml
# GitHub Actions 示例
name: Code Review
on: [pull_request]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install -g @anthropic-ai/claude-code
      - run: |
          git diff origin/main --name-only | \
          claude -p "审查以下变更文件中的代码质量和安全问题" \
                 --output-format json > review-report.json
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

### 批量处理脚本

```bash
#!/bin/bash
# 批量添加注释的脚本
for file in src/*.js; do
  if [ -f "$file" ]; then
    echo "处理文件: $file"
    claude -p "为 $file 中的每个函数添加 JSDoc 注释" --max-turns 5
  fi
done
```

### 代码生成

```bash
# 生成 API 文档
claude -p "从 src/routes/ 下的所有路由文件中生成 API 文档，输出 Markdown 格式" \
       --output-format json | jq -r '.text' > API.md
```

### 质量检查

```bash
# 代码质量检查脚本
claude -p "检查 src/ 目录下的代码质量：
1. 找出未使用的变量和函数
2. 检查命名规范是否一致
3. 识别潜在的代码异味" \
       --output-format json > quality-report.json
```

## 参数组合

```bash
# 常用参数组合
claude -p "请求内容" \
       --model claude-sonnet-4-20250514 \
       --max-turns 10 \
       --output-format json \
       --verbose

# 限制工具使用
claude -p "解释 package.json 的配置" \
       --allowedTools Read,Write

# 配置 MCP
claude -p "查询数据库表结构" \
       --mcp-config ./mcp-config.json
```

## 与交互模式的区别

| 特性 | 打印模式 (`-p`) | 交互模式 |
|------|-----------------|----------|
| 运行方式 | 单次执行后退出 | 持续对话 |
| 状态保持 | 无状态 | 有状态 |
| 适用场景 | 自动化、脚本、CI/CD | 交互式开发、调试 |
| 输出格式 | `text` 或 `json` | 格式化终端输出 |
| 管道输入 | 完全支持 | 不支持 |
| 多轮对话 | 不支持 | 支持 |
| 斜杠命令 | 不支持 | 支持 |
