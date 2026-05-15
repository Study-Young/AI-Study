# 一次性命令模式（One-Shot Mode）

`opencode run` 命令是 OpenCode 的一次性命令模式，让您无需进入交互式界面即可快速完成单个任务。这是日常开发中最常用的模式之一。

## 基本用法

```bash
opencode run '<您的提示词>'
```

OpenCode 会自动：
1. 分析当前项目结构
2. 理解上下文
3. 执行任务并输出结果
4. 退出（不会进入交互模式）

## 使用场景

### 代码生成

```bash
# 生成一个完整的函数
opencode run '创建一个 TypeScript 函数，用于验证电子邮件地址格式，包含单元测试'

# 生成配置文件
opencode run '为这个 Node.js 项目生成一个 Dockerfile，使用多阶段构建优化镜像大小'
```

### 代码解释与文档

```bash
# 解释代码
opencode run '解释 src/utils/database.ts 中每个函数的作用'

# 生成文档
opencode run '为 src/api/routes.ts 中的每个路由端点生成 JSDoc 注释'
```

### 代码审查

```bash
opencode run '审查最近修改的文件，检查潜在的安全漏洞和性能问题'
```

### 调试

```bash
opencode run '查看 package.json 和 tsconfig.json，检查 TypeScript 编译配置是否正确'
```

### 重构建议

```bash
opencode run '分析 src/components/ 目录下的组件，建议如何优化代码结构和减少重复'
```

## 高级用法

### 指定文件上下文

您可以在提示词中直接引用文件或目录：

```bash
# OpenCode 会自动分析这些文件
opencode run '比较 src/old.ts 和 src/new.ts，列出所有差异并评估变更影响'
```

### 管道输入

OpenCode 支持从标准输入读取内容：

```bash
# 将文件内容通过管道传递给 OpenCode
cat error.log | opencode run '分析这些错误日志，找出最常见的错误类型和可能的解决方案'

# 与 git diff 结合使用
git diff --cached | opencode run '根据以下 diff 编写提交信息'
```

### 结合 Shell 命令

```bash
# 查找文件并分析
find src -name "*.test.ts" | xargs opencode run '分析这些测试文件，评估测试覆盖率'

# 与 git 结合
git log --oneline -10 | opencode run '根据最近的提交记录，生成发布说明'
```

### 输出重定向

```bash
# 将结果保存到文件
opencode run '为这个项目生成 README.md' > README.md

# 或使用 tee 同时查看和保存
opencode run '生成项目的架构文档' | tee ARCHITECTURE.md
```

## 参数选项

```bash
opencode run --help
```

| 参数 | 说明 |
|------|------|
| `--provider <name>` | 指定 AI 提供商（覆盖配置文件） |
| `--model <name>` | 指定模型名称（覆盖配置文件） |
| `--context` | 包含项目上下文信息 |
| `--no-context` | 不包含项目上下文 |
| `--file <path>` | 将指定文件内容作为上下文 |
| `--temperature <float>` | 设置生成温度（0.0-1.0） |
| `--max-tokens <number>` | 设置最大输出 token 数 |
| `--cost` | 显示本次调用的费用信息 |
| `--json` | 以 JSON 格式输出 |

### 使用示例

```bash
# 使用特定模型
opencode run --provider openai --model gpt-4o '生成一个 React 组件'

# 设置较低的温度以获得更确定的结果
opencode run --temperature 0.1 '将这段 Python 代码转换为 TypeScript'

# 限制输出长度
opencode run --max-tokens 500 '简要总结这个项目的功能'

# 以 JSON 格式输出
opencode run --json '分析 package.json 并返回依赖关系结构'
```

## 工作流程示例

### 日常开发流程

```bash
# 1. 创建新功能
opencode run '在 src/services/ 下创建一个用户认证服务，包含登录、注册和 token 刷新功能'

# 2. 审查生成的代码
opencode run '审查 src/services/auth.ts，检查代码质量和安全性'

# 3. 生成测试
opencode run '为 src/services/auth.ts 编写完整的单元测试'

# 4. 更新文档
opencode run '更新 README.md，添加新的认证服务使用说明'
```

### 项目启动流程

```bash
# 初始化项目
mkdir my-project && cd my-project
git init

# 让 OpenCode 帮助你搭建项目
opencode run '创建一个 Next.js 项目，包含 TypeScript、Tailwind CSS 和 Prisma 数据库配置'
```

## 与 TUI 模式对比

| 特性 | 一次性命令模式 | 交互式 TUI 模式 |
|------|---------------|----------------|
| 适合场景 | 单个明确任务 | 多轮对话式开发 |
| 交互方式 | 一次输入，一次输出 | 持续对话 |
| 会话管理 | 自动创建新会话 | 可保存/恢复会话 |
| 实时流式输出 | ✅ | ✅ |
| 上下文连续性 | 每次独立 | 保持上下文 |
| 资源占用 | 低 | 中等 |
| 适用场景 | 自动化脚本、CI/CD | 复杂开发任务 |

## 提示

- 提示词越具体，结果越好。提供文件路径、行号等上下文信息
- 对复杂任务可以分解成多个连续的一次性命令
- 使用 `--cost` 参数追踪 AI 使用费用
- 结合 `tee` 或重定向将输出保存为项目文档
- 在 CI/CD 流程中使用一次性命令模式进行自动化代码审查和文档生成
