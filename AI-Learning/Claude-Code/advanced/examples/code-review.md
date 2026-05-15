# 实战示例：代码审查 (Code Review)

本文档演示如何使用 Claude Code 对项目代码进行全面审查。涵盖安全检查、性能分析、代码质量评估和自动化审查流程。

## 场景概述

对一个 Node.js + TypeScript 后端项目的 PR 进行代码审查，识别潜在问题并提供修复建议。

## 单文件代码审查

### 审查指定文件

```bash
claude -p "详细审查 src/services/auth.service.ts 文件：

审查要求：
1. 识别安全漏洞（认证绕过、注入等）
2. 检查性能问题（不必要的计算、内存泄漏等）
3. 评估代码质量（可读性、可维护性、SOLID 原则）
4. 检查错误处理（边界情况、异常处理）
5. 给出具体的修复建议和代码示例

每个问题请标注严重等级：CRITICAL / MAJOR / MINOR"
```

### 审查输出示例

```
┌─ 代码审查报告
│
│ 文件: src/services/auth.service.ts
│ 行数: 245
│ 审查日期: 2024-01-15
│
│ ┌─ 安全问题 (3)
│ │
│ ├─ [CRITICAL] 第 78 行：JWT Secret 硬编码
│ │   问题: JWT 密钥直接写在代码中
│ │   修复: 使用环境变量 process.env.JWT_SECRET
│ │   示例:
│ │     - const secret = 'hardcoded-secret-key'
│ │     + const secret = process.env.JWT_SECRET
│ │     + if (!secret) throw new Error('JWT_SECRET 未设置')
│ │
│ ├─ [CRITICAL] 第 156 行：SQL 注入风险
│ │   问题: 直接拼接 SQL 查询字符串
│ │   修复: 使用参数化查询
│ │   示例:
│ │     - const sql = `SELECT * FROM users WHERE id = '${userId}'`
│ │     + const sql = 'SELECT * FROM users WHERE id = $1'
│ │
│ └─ [MAJOR] 第 203 行：密码错误信息过于详细
│     问题: 返回的信息泄露了哪个字段错误
│     修复: 返回通用错误信息
│
│ ┌─ 性能问题 (2)
│ │
│ ├─ [MAJOR] 第 45 行：每次请求都查询数据库
│ │   问题: 用户信息查询未使用缓存
│ │   修复: 引入 Redis 缓存或内存缓存
│ │
│ └─ [MINOR] 第 120 行：不必要的数组拷贝
│     问题: 使用展开运算符拷贝大数组
│     修复: 使用 for 循环或直接引用
│
│ ┌─ 代码质量问题 (4)
│ │
│ ├─ [MAJOR] 第 30 行：函数超过 100 行
│ │   问题: handleLogin 函数过于庞大
│ │   修复: 拆分为多个小函数
│ │
│ ├─ [MAJOR] 缺少 TypeScript 类型定义
│ │   问题: 多个函数参数使用 any 类型
│ │
│ ├─ [MINOR] 第 55 行：魔法数字
│ │   问题: 直接使用数字 3600（应为常量）
│ │
│ └─ [MINOR] 缺少单元测试
│     问题: auth.service.ts 的测试覆盖率为 15%
│
└─ 总体评分: 6.5/10 (需要改进)
   建议优先修复 2 个 CRITICAL 安全问题
```

## 多文件项目审查

### 审查整个模块

```bash
claude -p "审查 src/services/ 目录中所有文件：

审查重点：
1. 模块间的依赖关系是否合理
2. 是否存在循环依赖
3. 各服务的职责是否单一
4. 接口定义是否一致
5. 重复代码识别
6. 整体架构评估

输出格式：先给出整体评估报告，再逐个文件详细分析"
```

### 审查比较两个版本

```bash
# 审查当前分支相对于 main 的变更
git diff origin/main --name-only > changed-files.txt
cat changed-files.txt | claude -p "审查以下文件列表的变更

对于每个变更文件：
1. 读取原文件和新文件的差异
2. 分析变更的目的和影响
3. 评估变更是否正确和安全
4. 给出改进建议

文件列表：
$(cat changed-files.txt)"
```

## 自动化代码审查脚本

### 基础审查脚本

创建 `scripts/code-review.sh`：

```bash
#!/bin/bash
# 自动化代码审查脚本
# 用法: ./code-review.sh [分支名]

set -e

TARGET_BRANCH="${1:-main}"
REPORT_DIR="code-review-reports"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
REPORT_FILE="${REPORT_DIR}/review-${TIMESTAMP}.md"

mkdir -p "$REPORT_DIR"

echo "========================================="
echo "  Claude Code 自动代码审查"
echo "  目标分支: $TARGET_BRANCH"
echo "  时间: $(date)"
echo "========================================="

# 获取变更文件列表
echo "正在获取变更文件..."
git fetch origin "$TARGET_BRANCH" 2>/dev/null || true
CHANGED_FILES=$(git diff origin/"$TARGET_BRANCH"...HEAD --name-only --diff-filter=ACM | grep -E '\.(ts|js|tsx|jsx|py|go|rs)$' || echo "")

if [ -z "$CHANGED_FILES" ]; then
  echo "没有需要审查的代码文件变更."
  exit 0
fi

echo "发现 $(echo "$CHANGED_FILES" | wc -l) 个代码文件变更"
echo ""

# 逐文件审查
for file in $CHANGED_FILES; do
  echo "正在审查: $file"

  # 获取文件的差异
  DIFF_CONTENT=$(git diff origin/"$TARGET_BRANCH"...HEAD -- "$file")

  # 使用 Claude Code 进行审查
  echo "$DIFF_CONTENT" | claude -p "请审查以下代码变更（diff 格式）：

文件路径: $file

审查要求：
1. 识别安全风险（CRITICAL 等级）
2. 发现逻辑错误和边界问题（MAJOR 等级）
3. 评估代码质量和性能（MINOR 等级）
4. 给出具体的修复建议

输出格式：
- 问题严重等级标签
- 问题描述
- 修复建议（含代码示例）" \
    --output-format json >> "$REPORT_FILE" 2>/dev/null

  echo "---" >> "$REPORT_FILE"
  echo "完成: $file"
done

echo ""
echo "审查完成！报告已保存到: $REPORT_FILE"
```

### 运行审查脚本

```bash
# 审查当前分支相对于 main 的变更
bash scripts/code-review.sh main

# 审查相对于特定分支的变更
bash scripts/code-review.sh develop

# 审查最近一次提交
bash scripts/code-review-git-commit.sh
```

## CI/CD 集成

### GitHub Actions 配置

创建 `.github/workflows/code-review.yml`：

```yaml
name: AI Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Get changed files
        id: changed-files
        run: |
          echo "files=$(git diff origin/${{ github.base_ref }}...HEAD --name-only --diff-filter=ACM | grep -E '\.(ts|js|tsx|jsx|py|go|rs)$' | tr '\n' ' ')" >> $GITHUB_OUTPUT

      - name: Run AI code review
        if: steps.changed-files.outputs.files != ''
        run: |
          claude -p "审查以下 PR 变更文件：
          $(git diff origin/${{ github.base_ref }}...HEAD --name-only)

          请重点关注：
          1. 安全漏洞
          2. 性能问题
          3. 逻辑错误
          4. 最佳实践遵循情况

          输出 JSON 格式审查报告" \
          --output-format json \
          --allowedTools Read,Grep \
          --max-turns 30 \
          --model claude-sonnet-4-20250514 > review-report.json
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}

      - name: Post review comment
        if: steps.changed-files.outputs.files != ''
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const report = JSON.parse(fs.readFileSync('review-report.json', 'utf8'));
            const body = `## 🤖 AI 代码审查报告\n\n${report.text}`;
            await github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: body
            });
```

## 审查要检查清单

### 安全审查清单

- [ ] SQL/NoSQL 注入防护
- [ ] XSS 跨站脚本防护
- [ ] CSRF 防护
- [ ] 认证和授权检查
- [ ] 敏感信息泄露（API Key、密码等）
- [ ] 输入验证
- [ ] 输出编码
- [ ] 不安全的反序列化
- [ ] 文件上传安全
- [ ] 依赖项安全漏洞

### 性能审查清单

- [ ] N+1 查询问题
- [ ] 不必要的数据库查询
- [ ] 缺少缓存
- [ ] 大循环中的性能瓶颈
- [ ] 内存泄漏风险
- [ ] 不必要的对象创建
- [ ] 异步操作未 await

### 代码质量审查清单

- [ ] SOLID 原则遵循
- [ ] DRY 原则（无重复代码）
- [ ] 函数/方法单一职责
- [ ] 合理的命名
- [ ] 适当的注释和文档
- [ ] 错误处理完整性
- [ ] 类型安全（TypeScript）
- [ ] 测试覆盖率
- [ ] 代码复杂度
