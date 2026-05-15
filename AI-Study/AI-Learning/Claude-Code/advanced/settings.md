# 设置与配置 (Settings)

Claude Code 提供多层次的配置系统，允许您精细控制其行为。本文档介绍配置层级、CLAUDE.md 项目指令文件和规则目录的使用方法。

## 配置层级

Claude Code 的配置遵循从全局到项目、从默认到具体的层级结构。较高优先级的设置会覆盖较低优先级的设置。

```
优先级从低到高：
┌─────────────────────────────────────────────┐
│  1. 内置默认值                                │  最低优先级
├─────────────────────────────────────────────┤
│  2. 全局配置文件 (~/.claude/config.json)      │
├─────────────────────────────────────────────┤
│  3. 项目配置文件 (.claude/config.json)        │
├─────────────────────────────────────────────┤
│  4. CLAUDE.md 项目指令                       │
├─────────────────────────────────────────────┤
│  5. 命令行参数 (--max-turns 等)               │  最高优先级
└─────────────────────────────────────────────┘
```

### 1. 内置默认值

Claude Code 自带合理的默认配置，无需任何配置即可使用。

### 2. 全局配置文件

全局配置文件位于 `~/.claude/config.json`，适用于当前用户的所有项目。

```json
{
  "model": "claude-sonnet-4-20250514",
  "maxTurns": 100,
  "allowedTools": ["Read", "Write", "Edit", "Bash", "ReadDir", "Glob", "Grep"],
  "temperature": 1.0,
  "maxTokens": 4096,
  "verbose": false
}
```

### 3. 项目配置文件

项目配置文件位于项目根目录下的 `.claude/config.json`，优先级高于全局配置。

```json
{
  "model": "claude-sonnet-4-20250514",
  "maxTurns": 50,
  "allowedTools": ["Read", "Write", "Edit"],
  "temperature": 0.3,
  "maxTokens": 2048,
  "verbose": false,
  "hooksEnabled": true
}
```

### 4. CLAUDE.md 项目指令

CLAUDE.md 是一个 Markdown 文件，放置于项目根目录（或 `.claude/` 目录中），用于为 Claude Code 提供项目特定的上下文和指令。

### 5. 命令行参数

命令行参数具有最高优先级，会覆盖所有其他配置。

## CLAUDE.md 项目指令文件

CLAUDE.md 是 Claude Code 理解项目的核心机制。它告诉 Claude Code 关于项目的重要信息。

### 文件位置

CLAUDE.md 可以放置在以下位置（按优先级从高到低）：

1. 当前工作目录下的 `.claude/CLAUDE.md`
2. 当前工作目录下的 `CLAUDE.md`
3. 项目根目录下的 `CLAUDE.md`
4. 父目录的 `CLAUDE.md`（递归向上查找）

### CLAUDE.md 内容结构

```markdown
# 项目名称

## 项目概述
简短描述项目的目标和用途。

## 技术栈
- 语言：TypeScript 5.x
- 框架：Next.js 14, React 18
- 数据库：PostgreSQL 16, Prisma ORM
- 测试：Jest, Playwright
- 构建：pnpm, Turborepo

## 项目结构
```
src/
├── app/           # Next.js App Router 路由
├── components/    # 共享 React 组件
│   ├── ui/        # 基础 UI 组件
│   └── features/  # 功能组件
├── lib/           # 工具函数和库
├── server/        # 服务器端代码
│   ├── api/       # API 路由
│   └── db/        # 数据库操作
└── types/         # TypeScript 类型定义
```

## 代码规范
- 使用 ESLint + Prettier 保持代码风格一致
- 组件使用函数式组件 + Hooks
- API 路由使用 Next.js Route Handlers
- 数据库查询使用 Prisma Client
- 所有函数必须有 TypeScript 类型注解
- 单元测试覆盖率不低于 80%

## 命名约定
- 组件文件：PascalCase (如 UserProfile.tsx)
- 工具函数：camelCase (如 formatDate.ts)
- API 路由：kebab-case (如 user-profile/)
- 数据库表：snake_case (如 user_profiles)
- CSS 类名：使用 CSS Modules (如 styles.container)

## 重要约定
- 不要直接修改数据库迁移文件
- API 路由需要认证中间件
- 所有用户输入必须经过验证和清理
- 错误信息使用中文返回
- 环境变量通过 .env.local 配置

## 常用命令
- `pnpm dev` - 启动开发服务器
- `pnpm build` - 构建生产版本
- `pnpm test` - 运行测试
- `pnpm lint` - 代码检查
- `pnpm db:migrate` - 数据库迁移
```

### CLAUDE.md 指令类型

| 指令类型 | 说明 | 示例 |
|----------|------|------|
| **项目概述** | 项目的基本信息和技术栈 | 描述项目目标和使用的技术 |
| **项目结构** | 目录结构和模块职责 | src/ 各目录的用途说明 |
| **代码规范** | 编码风格和最佳实践 | ESLint 规则、命名约定 |
| **工作流程** | 开发和部署流程 | 分支策略、CI 流程 |
| **重要提醒** | 需要特别注意的事项 | 安全规则、不可修改的文件 |
| **常用命令** | 开发相关的命令参考 | 构建、测试、部署命令 |

### 多平台指令

可以为不同平台或任务类型创建多个 CLAUDE.md 文件：

```markdown
# 项目指令

## 所有平台通用
- 遵循现有代码风格
- 保持向后兼容

## TypeScript 平台
- 使用 interface 而非 type（除非需要联合类型）
- 启用 strict 模式
- 避免使用 any 类型

## Python 平台
- 使用 type hints
- 遵循 PEP 8 规范
- 使用 pytest 编写测试
```

## 规则目录 (Rules Directory)

`.claude/rules/` 目录允许您将配置拆分成多个文件，便于管理和共享。

### 目录结构

```
.claude/
├── rules/
│   ├── code-style.md       # 代码风格规则
│   ├── security.md         # 安全规则
│   ├── testing.md          # 测试规则
│   ├── git-workflow.md     # Git 工作流规则
│   └── api-design.md       # API 设计规则
├── config.json
└── CLAUDE.md
```

### 规则文件示例

#### code-style.md

```markdown
# 代码风格规则

## TypeScript
- 使用 2 空格缩进
- 使用单引号
- 行尾添加分号
- 导入语句按以下顺序分组：
  1. 外部依赖 (react, next 等)
  2. 内部模块 (@/ 别名导入)
  3. 相对路径导入 (./, ../)
- 每个分组之间空一行

## CSS/Tailwind
- 使用 Tailwind CSS 类名
- 自定义样式写在 CSS Modules 中
- 颜色变量使用 CSS 自定义属性
```

#### security.md

```markdown
# 安全规则

## 通用规则
- 不要硬编码密钥或令牌
- 所有 API 端点需要认证
- 用户输入必须经过验证
- 使用参数化查询避免 SQL 注入

## 敏感数据
- 密码使用 bcrypt 哈希
- JWT 令牌设置在 httpOnly cookie 中
- 日志中不要记录敏感信息
```

#### testing.md

```markdown
# 测试规则

## 测试策略
- 单元测试覆盖所有工具函数
- 集成测试覆盖 API 端点
- E2E 测试覆盖关键用户流程

## 测试命名
- 单元测试：`describe('functionName')` + `it('should ...')`
- 集成测试：`describe('POST /api/users')` + `it('should ...')`
- 使用中文描述测试用例
```

## 配置合并规则

当多个配置源存在时，Claude Code 按以下规则合并：

1. **简单值**：高优先级覆盖低优先级
2. **数组**：高优先级替换整个数组（非合并）
3. **对象**：浅合并，高优先级覆盖同名键

```
全局配置:   { "model": "haiku", "maxTurns": 50, "allowedTools": ["Read", "Write"] }
项目配置:   { "maxTurns": 100, "verbose": true }
合并结果:   { "model": "haiku", "maxTurns": 100, "allowedTools": ["Read", "Write"], "verbose": true }
```

## 配置示例

### 前端项目配置

```json
{
  "model": "claude-sonnet-4-20250514",
  "allowedTools": ["Read", "Write", "Edit", "Grep", "Bash"],
  "temperature": 0.2,
  "maxTurns": 80
}
```

### 后端项目配置

```json
{
  "model": "claude-opus-4-20250514",
  "allowedTools": ["Read", "Write", "Edit", "Bash", "Grep"],
  "temperature": 0.1,
  "maxTurns": 120,
  "verbose": true
}
```

### 安全检查严格的项目

```json
{
  "model": "claude-opus-4-20250514",
  "allowedTools": ["Read", "Grep"],
  "temperature": 0.1,
  "maxTurns": 30,
  "hooksEnabled": true
}
```

## 查看当前配置

```bash
# 交互模式中
❯ /settings
```

输出示例：

```
当前设置：
  模型: claude-sonnet-4-20250514
  最大轮数: 100
  允许工具: Read, Write, Edit, Bash, ReadDir, Glob, Grep
  温度: 0.2
  最大 Token: 4096
  详细模式: false
  MCP 配置: 未设置
  钩子: 已启用

来源：
  全局: ~/.claude/config.json
  项目: .claude/config.json
  CLI: --max-turns (命令行覆盖)
```

## 修改配置

```bash
# 交互模式中修改设置
❯ /settings set temperature 0.5
已更新 temperature = 0.5

❯ /settings set model claude-opus-4-20250514
已更新 model = claude-opus-4-20250514

# 查看变更后的配置
❯ /settings
```
