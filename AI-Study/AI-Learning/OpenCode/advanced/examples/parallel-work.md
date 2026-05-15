# 并行工作示例

OpenCode 支持通过 Git Worktree 实现并行工作，让您同时处理多个独立任务，大幅提高开发效率。

## 概述

并行工作允许您：

- 同时处理多个独立的功能开发
- 在等待一个任务完成时切换到另一个任务
- 在不同分支上并行运行代码生成和测试
- 避免频繁的 Git stash 和分支切换

## 工作原理

OpenCode 使用 Git Worktree 技术，为每个任务创建独立的 Git 工作目录：

```
project-root/
├── .git/
├── src/              ← 主工作区（默认分支）
├── worktrees/
│   ├── feature-auth/ ← 认证功能工作区
│   ├── feature-api/  ← API 重构工作区
│   └── bugfix-crash/ ← 崩溃修复工作区
└── opencode.toml
```

每个工作区是独立的 Git 工作树，拥有自己的分支和文件系统，但共享同一个 Git 仓库。

## 基本用法

### 启动并行任务

```bash
# 创建一个新的并行任务
opencode parallel start "添加用户认证功能" --branch feature/auth

# 指定任务使用的代理
opencode parallel start "重构 API 路由" --branch refactor/api --agent architect
```

### 查看并行任务

```bash
# 列出所有进行中的任务
opencode parallel list

# 输出示例：
# ┌─────────────────────────────────────────────────────────┐
# │ 📋 并行任务列表                                         │
# ├───────┬──────────────┬──────────┬────────┬──────────────┤
# │ ID    │ 任务         │ 分支     │ 状态   │ 进度         │
# ├───────┼──────────────┼──────────┼────────┼──────────────┤
# │ 1     │ 用户认证功能 │ auth     │ 进行中 │ src/auth/    │
# │       │              │          │        │ 3/8 个文件   │
# ├───────┼──────────────┼──────────┼────────┼──────────────┤
# │ 2     │ API 重构     │ refactor │ 等待中 │ -            │
# ├───────┼──────────────┼──────────┼────────┼──────────────┤
# │ 3     │ 崩溃修复     │ hotfix   │ 已完成 │ 全部完成     │
# └───────┴──────────────┴──────────┴────────┴──────────────┘
```

### 切换工作区

```bash
# 切换到指定任务的工作区
opencode parallel switch 1

# 这会将您的工作目录切换到 worktrees/feature-auth/
# 您可以在该目录下独立工作
```

### 在任务中运行命令

```bash
# 在指定任务的工作区中运行命令
opencode parallel exec 1 -- "opencode run '创建用户模型和数据库迁移'"

# 在任务中运行 Shell 命令
opencode parallel exec 2 -- "npm test"

# 在所有进行中的任务中运行命令
opencode parallel exec --all -- "git status"
```

### 完成任务并合并

```bash
# 标记任务完成
opencode parallel complete 1

# OpenCode 会自动：
# 1. 提交工作区的变更
# 2. 推送到远程仓库
# 3. 删除临时工作树
# 4. 在主工作区创建合并请求（PR）
```

### 取消任务

```bash
# 取消并丢弃任务的所有变更
opencode parallel cancel 2

# 取消但保留工作区（方便后续恢复）
opencode parallel cancel 2 --keep
```

## 实际场景示例

### 场景一：同时开发多个功能

```bash
# 创建三个并行任务
opencode parallel start "实现用户登录 API" --branch feature/login
opencode parallel start "添加数据导出功能" --branch feature/export
opencode parallel start "优化首页加载性能" --branch perf/homepage

# 切换到第一个任务
opencode parallel switch 1
opencode run '创建用户登录的 REST API 端点，包含 JWT 认证'

# 在等待 AI 生成的间隙，切换到第二个任务
opencode parallel switch 2
opencode run '实现 CSV 和 Excel 数据导出功能'

# 再切换到第三个任务
opencode parallel switch 3
opencode run '分析首页性能瓶颈并优化'
```

### 场景二：并行开发与测试

```bash
# 在主分支上开发功能
opencode run '在 src/feature/ 下实现用户通知功能'

# 同时在独立分支上运行测试
opencode parallel start "运行测试套件" --branch test/notifications
opencode parallel exec -- "npm run test:coverage"
```

### 场景三：紧急修复与正常开发并行

```bash
# 正在开发新功能时收到紧急 bug 报告
# 创建一个 hotfix 分支
opencode parallel start "紧急修复登录崩溃" --branch hotfix/login-crash

# 切换到 hotfix 工作区处理紧急问题
opencode parallel switch 4
opencode run '分析 src/auth/login.ts 中的崩溃原因并修复'

# 修复完成后合并
opencode parallel complete 4

# 切换回原来的开发任务继续工作
opencode parallel switch 1
```

### 场景四：代码审查并行化

```bash
# 使用不同的代理并行审查代码
opencode parallel start "安全审查" --branch review/security
opencode parallel start "性能审查" --branch review/performance
opencode parallel start "代码风格审查" --branch review/style

# 在每个工作区中运行专门的审查
opencode parallel exec 1 -- "opencode pr-review --focus security"
opencode parallel exec 2 -- "opencode pr-review --focus performance"
opencode parallel exec 3 -- "opencode pr-review --focus style"

# 汇总审查结果
opencode parallel exec --all -- "cat review-report.md" > combined-review.md
```

## 高级用法

### 任务依赖

设置任务依赖关系，确保按顺序执行：

```bash
# 任务 2 依赖任务 1 完成
opencode parallel start "实现数据库模型" --branch db/models --id 1
opencode parallel start "实现业务逻辑" --branch service/logic --depends-on 1

# 当任务 1 完成后，任务 2 会自动开始
opencode parallel complete 1
# 输出: 任务 2 已自动启动，因为其依赖已完成
```

### 资源限制

控制并行任务的数量以避免系统过载：

```toml
# opencode.toml
[parallel]
# 最大并行任务数
max_tasks = 5

# 每个任务的最大工作区大小（MB）
max_workspace_size = 500

# 自动清理超过 N 天未活动的任务
auto_cleanup_days = 7

# 共享的 node_modules 目录（避免重复安装）
shared_node_modules = true
```

### 任务模板

创建可复用的任务模板：

```toml
# ~/.config/opencode/templates/feature.toml
[template]
name = "feature"
description = "新建功能开发任务"

[setup]
commands = [
  "npm install",
  "cp .env.example .env",
]

[review]
auto_review = true
review_focus = ["security", "performance"]

[completion]
auto_pr = true
pr_labels = ["feature", "auto-generated"]
```

```bash
# 使用模板创建任务
opencode parallel start "添加搜索功能" --template feature
```

## 配置选项

```toml
# opencode.toml
[parallel]
# 工作树存储目录
worktree_dir = ".worktrees"

# 默认基础分支
base_branch = "main"

# 自动提交
auto_commit = true

# 提交消息格式
commit_format = "conventional"

# 任务完成后的操作
# "ask" - 询问用户
# "pr"  - 自动创建 PR
# "merge" - 自动合并
on_complete = "ask"

# 通知设置
notifications = true

# 并行任务日志级别
log_level = "info"
```

## 最佳实践

### 任务粒度

- **每个任务只做一件事**：一个功能、一个修复或一个优化
- **任务边界清晰**：避免任务之间修改相同的文件
- **任务规模适中**：每个任务应在 1-2 小时内完成

### 工作流建议

```bash
# 1. 规划任务分解
opencode run '分析这个项目的结构，建议如何分解为独立的并行开发任务'

# 2. 创建任务
opencode parallel start "任务A"
opencode parallel start "任务B"
opencode parallel start "任务C"

# 3. 分配到不同工作区开发
# 任务A: 在前台交互开发
opencode parallel switch 1
opencode

# 任务B: 在后台运行自动化任务
opencode parallel exec 2 -- "opencode run '生成 API 文档'"

# 4. 定期同步进度
opencode parallel list

# 5. 完成任务并合并
opencode parallel complete 1
opencode parallel complete 2
opencode parallel complete 3
```

### 避免冲突

- 每个任务操作不同的文件或模块
- 使用接口定义（interface/types）隔离变更
- 定期将主分支的变更合并到各工作区

```bash
# 将主分支的最新变更同步到所有工作区
opencode parallel exec --all -- "git merge origin/main"
```

## 故障排除

### 工作树冲突

```bash
# 查看冲突详情
opencode parallel status --verbose

# 手动解决冲突
opencode parallel switch 2
# 进入工作区手动解决冲突
git mergetool
```

### 磁盘空间不足

```bash
# 清理已完成任务的工作区
opencode parallel cleanup

# 查看各工作区大小
opencode parallel du

# 手动删除工作区
opencode parallel cancel 3 --delete
```

### 任务卡住

```bash
# 强制终止任务
opencode parallel kill 2

# 重启任务
opencode parallel restart 2
```

## 下一步

- 学习创建 [自定义代理](../custom-agents.md) 与并行工作结合使用
- 探索 [PR 审核工作流](../pr-review.md) 集成到并行任务中
