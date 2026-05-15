# 交互模式

Claude Code 的交互模式（REPL - Read-Eval-Print Loop）是日常开发中最常用的工作方式。它提供持续对话环境、Tmux 集成、丰富的斜杠命令和键盘快捷键。

## 启动交互模式

```bash
# 最简单的启动方式
claude

# 启动时携带上下文
claude --model claude-sonnet-4-20250514

# 启动时指定配置文件
claude --configPath ./claude.json
```

进入交互模式后，您会看到如下界面：

```
╭────────────────────────────────────────╮
│  Claude Code 0.8.1                     │
│  输入您的问题，或使用 /help 查看命令    │
╰────────────────────────────────────────╯
❯
```

## REPL 基本用法

### 多轮对话

交互模式支持多轮对话，Claude Code 会记住之前的对话上下文：

```
❯ 创建一个 Python 计算器程序

┌─ 分析请求
│ 将创建 calculator.py 包含基本运算功能
│
┌─ 创建文件 calculator.py
│
└─ 完成！calculator.py 已创建

❯ 为它添加乘方和开方运算

┌─ 分析请求
│ 修改 calculator.py，添加乘方(**)和开方(sqrt)功能
│
└─ 完成！已更新 calculator.py
```

### 文件编辑

在交互模式中，Claude Code 可以直接编辑项目文件：

```
❯ 查看 src/app.js 的代码
❯ 将这个函数改写为 async/await 风格
❯ 在第 42 行添加注释说明这个类的用途
```

### 对话管理

```
❯ /clear          # 清除对话历史
❯ /reset          # 重置对话，清空上下文
❯ /undo           # 撤销上一步操作
```

## Tmux 集成

Claude Code 深度集成 tmux，提供无缝的终端会话管理。

### 自动检测

当在 tmux 会话中启动 Claude Code 时，它自动启用 tmux 集成：

```bash
# 创建或附加到 tmux 会话
tmux new -s my-session
# 在 tmux 中启动 Claude Code
claude
```

### Tmux 功能

| 功能 | 描述 |
|------|------|
| 自动分屏 | Claude Code 可在需要时自动创建分屏 |
| 命令执行 | 在另一个窗格中执行命令并观察输出 |
| 文件浏览 | 使用分屏查看文件内容 |
| 日志查看 | 在独立窗格中查看运行日志 |

### 手动配置 Tmux

在 `~/.tmux.conf` 中添加以下配置以获得最佳体验：

```tmux
# 启用鼠标支持
set -g mouse on

# 增加历史缓冲区
set -g history-limit 50000

# 设置分屏快捷键
bind | split-window -h
bind - split-window -v

# 使用 Ctrl+a 作为前缀（避免与终端快捷键冲突）
set -g prefix C-a
unbind C-b
bind C-a send-prefix
```

### Tmux 快捷键在 Claude Code 中的使用

```
前缀键 + %    垂直分屏
前缀键 + "    水平分屏
前缀键 + 方向键  切换窗格
前缀键 + z    全屏/恢复当前窗格
前缀键 + x    关闭当前窗格
前缀键 + [    进入滚动模式
```

## 斜杠命令

Claude Code 交互模式提供一系列斜杠命令（Slash Commands）：

### 对话控制

| 命令 | 别名 | 说明 |
|------|------|------|
| `/help` | `/h` | 显示帮助信息 |
| `/clear` | 无 | 清除当前对话历史 |
| `/reset` | 无 | 重置对话状态 |
| `/undo` | 无 | 撤销最近一次操作 |
| `/retry` | 无 | 重新执行上次请求 |

### 文件操作

| 命令 | 说明 |
|------|------|
| `/open <file>` | 打开并显示文件内容 |
| `/save <file>` | 将当前对话保存到文件 |
| `/edit <file>` | 编辑指定文件 |
| `/diff` | 显示最近更改的差异 |

### 信息查询

| 命令 | 说明 |
|------|------|
| `/status` | 显示当前状态和对话统计 |
| `/cost` | 显示当前对话的 token 使用量和费用 |
| `/model` | 显示当前使用的模型信息 |
| `/env` | 显示环境变量和配置信息 |

### 配置管理

| 命令 | 说明 |
|------|------|
| `/settings` | 显示当前设置 |
| `/settings set <key> <value>` | 修改设置项 |
| `/agents` | 列出可用的子代理 |
| `/agent <name>` | 切换到指定子代理 |

## 键盘快捷键

### 编辑快捷键

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+A` | 光标移动到行首 |
| `Ctrl+E` | 光标移动到行尾 |
| `Ctrl+U` | 删除光标到行首的内容 |
| `Ctrl+K` | 删除光标到行尾的内容 |
| `Ctrl+W` | 删除光标前的单词 |
| `Ctrl+L` | 清屏 |
| `Tab` | 自动补全（文件名或命令） |

### 导航快捷键

| 快捷键 | 功能 |
|--------|------|
| `↑` | 上一条命令历史 |
| `↓` | 下一条命令历史 |
| `Ctrl+R` | 搜索命令历史 |
| `Ctrl+B` / `←` | 光标左移 |
| `Ctrl+F` / `→` | 光标右移 |

### 会话控制

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+C` | 取消当前操作/响应 |
| `Ctrl+D` | 退出交互模式 |
| `Ctrl+Z` | 挂起 Claude Code |
| `Ctrl+S` | 暂停输出（恢复按 `Ctrl+Q`） |
| `Ctrl+T` | 查看运行时间统计 |

## 高级交互技巧

### 多文件操作

```
❯ 同时查看 src/components/Header.tsx 和 src/components/Footer.tsx，
   然后修改它们使其使用相同的主题颜色变量
```

### 项目级重构

```
❯ 扫描整个 src/ 目录，找出所有硬编码的颜色值，
   提取到 theme.css 文件中，并更新所有引用
```

### 调试会话

```
❯ 帮我调试这个错误：
   当我运行 npm run build 时出现以下错误：
   [粘贴错误信息]
   请逐步诊断并修复
```

### 使用上下文文件

在启动时引用特定的上下文文件：

```bash
# 启动时加载项目约定文件
claude

# 然后在交互模式中
❯ 请参考 .claude/CONTEXT.md 中的项目约定，审查 src/ 下的新代码
```

## 退出交互模式

```bash
❯ /exit
# 或按 Ctrl+D
# 或输入 exit 或 quit
```
