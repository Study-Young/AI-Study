# 快速入门指南

本文档帮助您快速上手 Claude Code，从简单的 Hello World 示例开始，逐步了解基本用法和核心功能。

## "Hello World" 示例

### 最简单的用法

在终端中直接运行 Claude Code 来处理文件：

```bash
# 创建一个简单的 HTML 文件
claude -p "创建一个 hello.html 文件，包含一个蓝色的 Hello World 标题和一段描述文字"
```

Claude Code 会自动创建文件并写入内容。输出示例：

```
┌─ 分析请求
│
┌─ 创建文件 hello.html
│
┌─ 任务完成！
│
└─ 已将以下文件写入磁盘：
     hello.html
```

### 使用交互模式

```bash
# 进入交互模式
claude
```

然后在交互式 REPL 中输入：

```
❯ 创建一个 hello.py 文件，内容为：
   print("Hello, World!")
   print("欢迎使用 Claude Code！")
```

Claude Code 会创建文件并在完成后给出反馈。

### 使用管道输入

将标准输入通过管道传递给 Claude Code：

```bash
# 从文件读取内容并让 Claude 分析
cat error.log | claude -p "分析这个日志文件中的错误模式并给出修复建议"

# 使用 here 文档
claude -p "解释以下代码的作用" << 'EOF'
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}
console.log(fibonacci(10));
EOF
```

## 基本工作流程

### 1. 初始化项目

```bash
# 进入您的项目目录
cd my-project

# 启动 Claude Code
claude
```

### 2. 常用操作示例

#### 文件操作

```bash
# 创建多个文件
claude -p "创建一个简单的博客项目，包含 index.html、style.css 和 script.js"

# 修改现有文件
claude -p "在 README.md 中添加项目安装说明部分"

# 代码优化
claude -p "重构 src/utils.js 中的代码，提高可读性和性能"
```

#### 代码审查

```bash
# 审查单个文件
claude -p "审查 src/app.js 中的代码，找出潜在问题并提出改进建议"

# 审查整个项目
claude -p "对整个 src/ 目录进行代码审查，重点关注安全漏洞和性能问题"
```

#### 调试辅助

```bash
# 分析错误
claude -p "当我运行 npm test 时出现以下错误：[粘贴错误信息]。请分析原因并给出修复方案"

# 解释代码
claude -p "解释 src/services/auth.js 中的认证逻辑"
```

### 3. 使用打印模式（一次性任务）

```bash
# 快速提问，无需创建文件
claude -p "Python 中列表推导式和生成器表达式的区别是什么？"

# 格式化输出
claude -p "给我 5 个实用的 git 命令" --output-format json
```

## 命令行参数速览

| 参数 | 简写 | 说明 |
|------|------|------|
| `-p` | `--print` | 打印模式，处理请求后直接退出 |
| `--model` | 无 | 指定使用的模型 |
| `--max-turns` | 无 | 最大交互轮数 |
| `--output-format` | 无 | 输出格式（text/json） |
| `--allowedTools` | 无 | 限制允许使用的工具 |
| `--agents` | 无 | 使用自定义子代理 |

## 真实场景示例

### 创建并运行一个 Web 应用

```bash
claude -p "创建一个简单的 Express.js Web 服务器，包含：
- 一个 GET / 路由返回 'Hello, Claude!'
- 一个 GET /api/time 路由返回当前时间
- 一个 POST /api/echo 路由回显请求体
- 包含错误处理中间件"
```

### 数据库操作

```bash
claude -p "查看 database/schema.sql，然后：
1. 解释数据库表结构
2. 为 users 表编写一个插入数据的迁移脚本
3. 添加必要的索引"
```

### 自动化测试

```bash
claude -p "为 src/calculator.js 中的函数编写单元测试，使用 Jest 框架：
1. 测试所有主要功能
2. 包括边界条件测试
3. 包含测试覆盖率配置"
```

## 键盘快捷键

交互模式下常用的快捷键：

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+C` | 取消当前操作 |
| `Ctrl+D` | 退出交互模式 |
| `Ctrl+L` | 清屏 |
| `↑` / `↓` | 浏览命令历史 |
| `Tab` | 自动补全（如果支持） |

## 下一步

完成快速入门后，建议您：

- 阅读 **[交互模式](interactive-mode.md)** 文档，深入了解 REPL 使用技巧
- 阅读 **[打印模式](print-mode.md)** 文档，掌握自动化任务处理
- 阅读 **[常用参数](common-flags.md)** 文档，了解所有命令行选项
- 查看 **[高级主题](../advanced/agents.md)**，学习如何创建自定义子代理
