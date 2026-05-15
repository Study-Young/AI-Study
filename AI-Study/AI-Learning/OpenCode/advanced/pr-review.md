# PR 审核工作流

OpenCode 提供自动化的 PR（Pull Request）审核工作流，帮助团队在代码审查过程中提高效率和一致性。

## 概述

PR 审核工作流可以：

- 自动审查新提交的 PR
- 检测安全漏洞、性能问题、代码风格偏差
- 提供具体的改进建议和代码示例
- 生成结构化的审查报告
- 与 Git 平台集成（GitHub、GitLab 等）

## 基本用法

### 审查单个 PR

```bash
# 审查当前分支相对于主分支的变更
opencode pr-review

# 审查特定 PR（GitHub）
opencode pr-review --pr 42

# 审查特定分支
opencode pr-review --branch feature/new-api
```

### 审查本地未提交的变更

```bash
# 审查工作区中的未暂存变更
opencode pr-review --staged

# 审查暂存的变更
opencode pr-review --cached
```

## 配置 PR 审核

### 基础配置

```toml
# opencode.toml
[git.pr_review]
# 审核触发方式
# "always" - 每次运行都审核
# "on-open" - 仅打开 PR 时审核
# "manual" - 仅手动触发
auto_review = "on-open"

# 审核重点领域
# "security", "performance", "style", "maintainability", "all"
focus = "all"

# 最大审查文件数（超出时只审查最重要的文件）
max_files = 20

# 最大审查行数
max_lines = 5000

# 忽略的文件模式
ignore_patterns = [
  "*.lock",
  "*.min.*",
  "generated/**",
  "vendor/**",
]

# 审核严重级别
# "critical" - 仅严重问题
# "important" - 重要问题及以上
# "all" - 所有问题
severity = "important"
```

### 自定义审核规则

```toml
[git.pr_review.rules]
# 强制规则（必须满足）
enforce = [
  "no-hardcoded-secrets",
  "no-sql-injection",
  "error-handling-required",
  "test-coverage-minimum",
]

# 建议规则（建议满足）
suggest = [
  "use-const-instead-of-let",
  "add-jsdoc-comments",
  "prefer-early-return",
  "max-function-length",
]

# 安全规则
security = [
  "sanitize-user-input",
  "validate-authentication",
  "check-authorization",
  "audit-dependencies",
]
```

## 运行 PR 审核

### 本地审核

```bash
# 完整审核
opencode pr-review

# 指定审核重点
opencode pr-review --focus security

# 生成 HTML 报告
opencode pr-review --format html --output review-report.html

# 生成 Markdown 报告
opencode pr-review --format markdown > pr-review.md

# JSON 格式输出（便于集成）
opencode pr-review --format json > review-result.json
```

### CI/CD 集成

#### GitHub Actions

```yaml
# .github/workflows/pr-review.yml
name: OpenCode PR Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 22

      - name: Install OpenCode
        run: npm i -g opencode-ai@latest

      - name: Run PR Review
        run: |
          opencode pr-review \
            --github-token ${{ secrets.GITHUB_TOKEN }} \
            --focus security \
            --format markdown \
            --comment
        env:
          OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
```

#### GitLab CI

```yaml
# .gitlab-ci.yml
opencode-pr-review:
  stage: review
  image: node:22
  script:
    - npm i -g opencode-ai@latest
    - opencode pr-review
      --gitlab-token $CI_JOB_TOKEN
      --focus all
      --comment
  only:
    - merge_requests
  variables:
    OPENROUTER_API_KEY: $OPENROUTER_API_KEY
```

## 审核报告解读

### 报告结构

审核报告包含以下部分：

```
# OpenCode PR 审核报告

## 概览
- 分支: feature/new-api → main
- 变更文件: 12 个文件，+342/-89 行
- 审核重点: 全部
- 审核时间: 2026-05-15 10:30:00

## 严重问题 (2)
### 🔴 [SEC-001] API 密钥硬编码
- 文件: src/config.ts:42
- 严重性: 严重
- 描述: API 密钥 'sk-xxx123' 直接硬编码在源代码中
- 建议: 使用环境变量或密钥管理服务
- 修复示例:
  ```diff
  - const API_KEY = 'sk-xxx123';
  + const API_KEY = process.env.API_KEY;
  ```

### 🔴 [SEC-002] SQL 注入风险
- 文件: src/db/users.ts:15
- 严重性: 严重
- 描述: 直接拼接用户输入到 SQL 查询
- 建议: 使用参数化查询
- 修复示例:
  ```diff
  - db.query(`SELECT * FROM users WHERE id = ${userId}`);
  + db.query('SELECT * FROM users WHERE id = ?', [userId]);
  ```

## 重要问题 (5)
### 🟡 [PERF-001] 循环中重复数据库查询
- 文件: src/services/order.ts:23-30
- 严重性: 重要
- 描述: 在 for 循环中每次迭代都查询数据库
- 建议: 使用批量查询或 N+1 查询优化

### 🟡 [STYLE-003] 不一致的命名规范
...

## 建议 (8)
### 🔵 [MAINT-001] 函数过长
...

## 统计
- 总问题数: 15
- 严重: 2
- 重要: 5
- 建议: 8
- 通过率: 87%
```

### 严重级别

| 级别 | 标识 | 含义 |
|------|------|------|
| 严重 | 🔴 | 必须修复，否则不应合并 |
| 重要 | 🟡 | 建议修复，影响代码质量 |
| 建议 | 🔵 | 最佳实践建议，可选择性采纳 |

### 问题类别

| 类别 | 标识 | 说明 |
|------|------|------|
| 安全 | SEC | 安全漏洞和风险 |
| 性能 | PERF | 性能问题和优化机会 |
| 风格 | STYLE | 代码风格和格式问题 |
| 可维护性 | MAINT | 代码可读性和可维护性 |
| 可靠性 | REL | 潜在的运行时错误 |
| 测试 | TEST | 测试覆盖率和测试质量 |

## 高级配置

### 自定义审核代理

使用 [自定义代理](custom-agents.md) 进行更专业的审核：

```toml
# opencode.toml
[git.pr_review]
agent = "security-audit"  # 使用安全审计代理
```

```bash
# 或通过命令行指定
opencode pr-review --agent security-audit
```

### 增量审核

只审核新增的变更，避免重复审查未修改的代码：

```bash
opencode pr-review --incremental
```

### 批量审核

```bash
# 审核多个 PR
opencode pr-review --prs 42,43,44 --batch

# 审核指定日期以来的所有 PR
opencode pr-review --since "2026-05-01"
```

### 自动修复

对于某些类型的问题，OpenCode 可以自动生成修复补丁：

```bash
opencode pr-review --auto-fix
```

这会在审核报告中包含可直接应用的补丁。

## 最佳实践

### 团队工作流

1. **设置 CI 集成** — 在 CI/CD 中配置自动审核
2. **定义审核标准** — 根据团队需求配置审核规则
3. **分级处理** — 严重问题必须修复，重要问题建议修复
4. **定期回顾** — 定期分析审核数据，改进团队代码质量

### 审核前准备

```bash
# 确保分支是最新的
git fetch origin
git rebase origin/main

# 运行测试
npm test

# 运行 lint
npm run lint

# 然后再进行 PR 审核
opencode pr-review
```

### 与自定义代理结合

```bash
# 使用不同代理进行多角度审核
opencode pr-review --agent security-audit --focus security
opencode pr-review --agent performance-expert --focus performance
opencode pr-review --agent code-reviewer --focus all
```

## 故障排除

### 审核范围过大

```bash
# 限制审查文件数
opencode pr-review --max-files 10

# 指定审查目录
opencode pr-review --path src/api/
```

### 审核结果不准确

```bash
# 调整模型参数
opencode pr-review --temperature 0.2

# 使用更强的模型
opencode pr-review --model "anthropic/claude-sonnet-20241022"
```

### API 密钥权限

确保 API 密钥具有访问 Git 平台的权限：

```bash
# GitHub
export GITHUB_TOKEN="ghp_xxx"

# GitLab
export GITLAB_TOKEN="glpat-xxx"
```

## 下一步

- 学习创建 [自定义代理](custom-agents.md) 以定制审核标准
- 查看 [并行工作示例](examples/parallel-work.md) 了解高级工作流
