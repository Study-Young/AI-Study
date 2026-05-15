# 自定义子代理 (Agents)

Claude Code 支持自定义子代理（Agents），允许您创建具有特定角色、行为方式和工作流程的专用 AI 助手。子代理可以在不同任务类型之间快速切换，提高工作效率。

## 子代理概述

子代理是通过预定义的提示词（system prompt）和行为约束来定制的 Claude Code 实例。每个子代理可以专注于特定的任务领域。

```
┌─────────────────────────────────┐
│         Claude Code              │
│  ┌──────────┐ ┌──────────┐      │
│  │ Agent A  │ │ Agent B  │      │
│  │ (代码审查)│ │ (测试编写)│      │
│  └──────────┘ └──────────┘      │
│  ┌──────────┐ ┌──────────┐      │
│  │ Agent C  │ │ Agent D  │      │
│  │ (文档生成)│ │ (安全审计)│      │
│  └──────────┘ └──────────┘      │
└─────────────────────────────────┘
```

## 创建子代理

### 配置文件方式

在项目根目录下的 `.claude/agents/` 目录中创建子代理配置文件：

```bash
mkdir -p .claude/agents
```

#### 代码审查代理示例

创建 `.claude/agents/code-reviewer.json`：

```json
{
  "name": "code-reviewer",
  "description": "专业的代码审查代理，专注于代码质量、安全性和性能",
  "prompt": "你是一位资深代码审查专家。具有 15 年以上软件开发经验。\n\n审查要求：\n1. 始终先理解整体架构再审查细节\n2. 重点关注：安全漏洞 > 性能问题 > 代码质量 > 风格问题\n3. 每个问题必须附带严重等级（CRITICAL / MAJOR / MINOR）\n4. 给出具体的修复建议和代码示例\n5. 遵循 SOLID 原则和 DRY 原则\n\n输出格式：\n- 使用 Markdown 格式\n- 按严重等级排序问题\n- 每条问题包含：位置、问题描述、风险等级、修复建议",
  "allowedTools": ["Read", "Grep", "Glob"],
  "temperature": 0.2,
  "model": "claude-sonnet-4-20250514"
}
```

#### 测试编写代理示例

创建 `.claude/agents/test-writer.json`：

```json
{
  "name": "test-writer",
  "description": "自动编写高质量单元测试和集成测试",
  "prompt": "你是一位测试驱动开发（TDD）专家。\n\n测试编写规范：\n1. 为每个函数/方法编写单元测试\n2. 包含正常路径、边界条件和异常路径测试\n3. 使用 arrange-act-assert 模式\n4. 测试命名遵循 given_when_then 格式\n5. 保持测试独立，不互相依赖\n6. 模拟外部依赖\n\n框架偏好（按优先级）：\n- JavaScript/TypeScript: Jest\n- Python: pytest\n- Go: testing\n- Rust: cargo test",
  "allowedTools": ["Read", "Write", "Edit", "Bash"],
  "temperature": 0.3
}
```

#### 文档生成代理示例

创建 `.claude/agents/doc-writer.json`：

```json
{
  "name": "doc-writer",
  "description": "从代码中自动生成项目文档和 API 文档",
  "prompt": "你是一位技术文档专家。\n\n文档编写规范：\n1. 从源代码中提取类型签名和注释生成文档\n2. 使用清晰的中文编写说明文档\n3. 包含使用示例代码\n4. 标注参数说明、返回值和异常\n5. 遵循 JSDoc / reStructuredText / GoDoc 等标准格式\n\n输出格式偏好：\n- 项目文档：Markdown\n- API 文档：OpenAPI 3.0 (Swagger)\n- 类型文档：TypeScript 类型声明",
  "allowedTools": ["Read", "Glob", "Grep", "Write"],
  "temperature": 0.4
}
```

#### 安全审计代理示例

创建 `.claude/agents/security-auditor.json`：

```json
{
  "name": "security-auditor",
  "description": "安全审计专家，检查 OWASP Top 10 和其他安全风险",
  "prompt": "你是一位信息安全专家，精通 OWASP Top 10、CWE 标准和渗透测试。\n\n安全检查清单：\n1. SQL 注入\n2. XSS 跨站脚本\n3. CSRF 跨站请求伪造\n4. 认证和授权漏洞\n5. 敏感信息泄露\n6. 不安全的直接对象引用\n7. 安全配置错误\n8. 不安全的反序列化\n9. 使用已知漏洞的组件\n10. 日志和监控不足\n\n每个发现必须包含：CVE/CWE 编号、风险评分（CVSS）、复现步骤",
  "allowedTools": ["Read", "Grep", "Glob"],
  "temperature": 0.1,
  "model": "claude-opus-4-20250514"
}
```

### 子代理配置字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 代理名称，用于在命令中引用 |
| `description` | string | 是 | 代理描述，显示在 /agents 列表中 |
| `prompt` | string | 是 | 系统提示词，定义代理的角色和行为 |
| `allowedTools` | string[] | 否 | 限制代理可用的工具列表 |
| `temperature` | number | 否 | 输出的随机性（0-2），默认 1.0 |
| `model` | string | 否 | 指定该代理使用的模型 |

## 使用子代理

### 列出可用代理

```bash
# 在交互模式中
claude
❯ /agents
```

输出示例：

```
可用代理：
  default          - 通用 Claude Code 助手
  code-reviewer    - 专业的代码审查代理
  test-writer      - 自动编写高质量单元测试和集成测试
  doc-writer       - 从代码中自动生成项目文档和 API 文档
  security-auditor - 安全审计专家
```

### 切换代理

```bash
# 在交互模式中切换
❯ /agent code-reviewer
切换到代理: code-reviewer (专业的代码审查代理)

# 返回默认代理
❯ /agent default
切换到代理: default
```

### 启动时指定代理

```bash
# 使用配置文件中定义的代理
claude --agents .claude/agents/code-reviewer.json

# 使用 JSON 字符串直接定义代理
claude --agents '{"my-agent": {"description": "自定义代理", "prompt": "你是一个..."}}'
```

## 动态子代理

您可以在启动时通过 `--agents` 参数动态创建临时子代理，无需预先创建配置文件。

### 方式和示例

```bash
# 单代理
claude --agents '{"translator": {"description": "翻译助手", "prompt": "你是一位专业翻译，将英文技术文档翻译为中文。保持技术术语的准确性。"}}'

# 多代理配置
claude --agents '{
  "translator": {
    "description": "翻译助手",
    "prompt": "你是一位专业翻译，将英文技术文档翻译为中文。"
  },
  "teacher": {
    "description": "教学助手",
    "prompt": "你是一位编程导师，用简单易懂的方式解释复杂概念。",
    "temperature": 0.7
  }
}'
```

### 与交互模式配合

```bash
claude --agents '{"python-expert": {"description": "Python 专家", "prompt": "你是 Python 核心开发者，精通 CPython 内部实现。"}}'
# 进入交互模式后
❯ /agent python-expert
❯ 解释 Python 的 GIL 是如何实现的
```

## 子代理工作流程示例

### 完整项目开发流程

```bash
# 1. 安全审计
claude --agents .claude/agents/security-auditor.json -p "审计 src/ 目录的代码安全"

# 2. 代码审查
claude --agents .claude/agents/code-reviewer.json -p "审查新提交的代码"

# 3. 编写测试
claude --agents .claude/agents/test-writer.json -p "为 src/services/auth.js 编写测试"

# 4. 生成文档
claude --agents .claude/agents/doc-writer.json -p "为 src/api/ 生成 API 文档"
```

### 交互模式中的代理切换

```
❯ /agent code-reviewer
切换到代理: code-reviewer
❯ 审查 src/app.js 中的认证逻辑

...审查结果输出...

❯ /agent test-writer
切换到代理: test-writer
❯ 为刚才审查的认证逻辑编写测试

...测试代码生成...

❯ /agent doc-writer
切换到代理: doc-writer
❯ 为认证模块生成 API 文档
```

## 最佳实践

1. **单一职责**：每个子代理应该专注于一个特定领域
2. **明确的提示词**：提示词越具体，子代理的表现越好
3. **工具限制**：限制不需要的工具可以提高安全性
4. **温度控制**：代码生成使用低温度（0.1-0.3），创意任务使用高温度（0.7-1.0）
5. **模型选择**：复杂任务使用更强的模型（Opus），简单任务使用轻量模型（Haiku）
6. **版本管理**：将 `.claude/agents/` 目录纳入版本控制，团队共享代理配置
