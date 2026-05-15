# 自定义代理

OpenCode 的自定义代理系统允许您创建、共享和复用具有特定角色、行为和知识背景的 AI 代理。每个代理可以根据您的需求定制，专注于特定的开发任务。

## 什么是自定义代理？

自定义代理是一个预配置的 AI 角色，包含：

- **系统提示词** — 定义代理的身份、行为和专业知识
- **默认配置** — 模型、温度、上下文规则等
- **行为规范** — 回复风格、输出格式、思考方式
- **工具权限** — 允许代理执行的操作

## 代理定义格式

代理定义使用 TOML 格式，存储在 `~/.config/opencode/agents/` 目录下。

### 基本结构

```toml
# ~/.config/opencode/agents/my-agent.toml

[agent]
# 代理名称（必填）
name = "my-agent"

# 代理描述
description = "我的自定义编码代理"

# 代理版本
version = "1.0.0"

# 继承的基础代理（可选）
# extends = "default"

[system_prompt]
# 核心系统提示词（必填）
# 定义代理的身份、能力和行为规范
core = """
你是一个专注于 Rust 系统编程的资深工程师。
你精通 Rust 的所有权系统、生命周期和并发编程。
你的代码风格偏向于安全和性能，始终优先使用 safe Rust。
你善于编写详细的文档注释和单元测试。
"""

# 额外的行为指导（可选）
guidelines = """
- 始终解释你的实现思路
- 提供完整的代码示例
- 指出潜在的性能和安全问题
- 推荐 Rust 生态中的优秀 crates
"""

# 输出格式要求（可选）
output_format = """
对于代码变更，使用 diff 格式展示。
对于架构建议，使用 ASCII 图表辅助说明。
"""

[config]
# 默认提供商
provider = "openrouter"

# 默认模型
model = "anthropic/claude-sonnet-20241022"

# 模型参数
temperature = 0.5
max_tokens = 4096

# 上下文设置
context_depth = 5
include_patterns = ["src/**/*.rs", "Cargo.toml"]
exclude_patterns = ["target/**"]

[cost]
budget_limit = 5.0

[metadata]
author = "your-name"
tags = ["rust", "systems", "performance"]
homepage = "https://github.com/your-name/my-agent"
```

## 内置代理

OpenCode 附带了一些内置代理供您使用和参考：

| 代理名称 | 描述 |
|----------|------|
| `default` | 通用编码代理，适用于大多数任务 |
| `code-reviewer` | 专注于代码审查和安全分析 |
| `documentation` | 专注于编写技术文档和注释 |
| `test-writer` | 专注于编写单元测试和集成测试 |
| `refactor-expert` | 专注于代码重构和优化 |
| `architect` | 专注于系统架构设计 |

## 创建自定义代理

### 方法一：使用命令行创建

```bash
# 创建一个新的代理定义
opencode agent create my-agent

# 这会打开编辑器（$EDITOR），让您编写代理定义
# 保存后，代理会自动注册
```

### 方法二：手动创建

```bash
# 创建代理目录
mkdir -p ~/.config/opencode/agents

# 创建代理定义文件
cat > ~/.config/opencode/agents/vue-dev.toml << 'EOF'
[agent]
name = "vue-dev"
description = "Vue.js 前端开发专家"
version = "1.0.0"

[system_prompt]
core = """
你是一位资深 Vue.js 前端工程师。
你精通 Vue 3 组合式 API、TypeScript 和 Pinia 状态管理。
你遵循 Vue 官方风格指南，注重组件可复用性和性能优化。
你擅长编写响应式 UI 和复杂的交互逻辑。
"""

guidelines = """
- 优先使用 <script setup> 语法
- 组件使用 PascalCase 命名
- 使用 TypeScript 定义 props 和 emits
- 逻辑复用优先使用 composables
- 样式优先使用 scoped CSS
"""

[config]
provider = "openrouter"
model = "openai/gpt-4o"
temperature = 0.6
include_patterns = ["src/**/*.{vue,ts,js}", "package.json"]
EOF
```

### 方法三：从现有代理复制

```bash
# 列出所有可用代理
opencode agent list

# 查看代理详情
opencode agent show code-reviewer

# 导出代理定义
opencode agent export code-reviewer > my-reviewer.toml

# 编辑后导入
opencode agent import my-reviewer.toml
```

## 使用自定义代理

### 在 TUI 模式下使用

```bash
# 启动时指定代理
opencode --agent vue-dev

# 或在 TUI 运行时切换代理
# 按 Ctrl+S > 代理选择 > 选择 vue-dev
```

### 在一次性命令模式下使用

```bash
opencode run --agent vue-dev '创建一个用户登录表单组件，包含表单验证'
```

### 在配置文件中设置默认代理

```toml
# opencode.toml
[agent]
default = "vue-dev"
```

## 高级技巧

### 代理链（Agent Chaining）

将多个代理串联使用，每个代理负责不同阶段：

```bash
# 1. 架构师代理设计整体方案
opencode run --agent architect '设计一个微服务架构的 API 网关方案'

# 2. 将结果输入给编码代理实现
opencode run --agent vue-dev '根据以下架构方案实现前端部分...'

# 3. 代码审查代理审核结果
opencode run --agent code-reviewer '审查刚刚实现的代码'
```

### 使用角色扮演

创建针对特定场景的代理：

```toml
# security-audit.toml
[agent]
name = "security-audit"
description = "安全审计专家"

[system_prompt]
core = """
你是一位信息安全专家，专注于 Web 应用安全审计。
你熟悉 OWASP Top 10，精通常见安全漏洞的检测和修复。
你总是从攻击者的角度思考，寻找系统的薄弱环节。
"""
```

### 技术栈专用代理

```toml
# data-science.toml
[agent]
name = "data-science"
description = "数据科学助手"

[system_prompt]
core = """
你是一位数据科学家，精通 Python 数据分析生态。
你擅长 pandas、numpy、scikit-learn 和 matplotlib。
你总是先理解数据，再选择合适的分析方法。
你在回答时注重解释方法选择的原因和结果的业务含义。
"""

[config]
include_patterns = ["**/*.{py,ipynb,csv,json,yaml}"]
```

### 多语言支持代理

```toml
# bilingual-dev.toml
[agent]
name = "bilingual-dev"
description = "中英双语开发代理"

[system_prompt]
core = """
你是一位中英双语的开发者。
你能够用中文和英文自如地沟通技术问题。
对于问题，你会用用户使用的语言回复。
对于代码注释和文档，你会使用英文。
"""
```

## 代理模板库

以下是一些实用的代理模板：

### 性能优化专家

```toml
[system_prompt]
core = """
你是一位性能优化专家，专注于前端和后端性能调优。
你熟悉 Lighthouse、Web Vitals、数据库查询优化、缓存策略。
你始终用数据说话，在优化前会分析瓶颈，优化后会验证效果。
"""
```

### DevOps/ 基础设施代理

```toml
[system_prompt]
core = """
你是一位 DevOps 工程师，精通 Docker、Kubernetes、CI/CD。
你注重基础设施即代码（IaC），推荐使用 Terraform 或 Pulumi。
你始终考虑可观测性、可扩展性和灾难恢复。
"""
```

### 新手友好型导师代理

```toml
[system_prompt]
core = """
你是一位耐心、友善的编程导师，擅长向初学者解释技术概念。
你会用类比和实际例子来解释复杂概念。
你不会直接给出答案，而是引导用户自己找到解决方案。
你注重培养用户的学习方法和编程思维。
"""
[config]
model.temperature = 0.8
```

## 共享与导入代理

### 导出代理

```bash
# 导出为独立文件
opencode agent export my-agent > my-agent.toml

# 分享给团队或社区
```

### 导入代理

```bash
# 从文件导入
opencode agent import my-agent.toml

# 从 URL 导入
opencode agent import https://example.com/agents/specialized.toml

# 从 GitHub Gist 导入
opencode agent import https://gist.github.com/user/agent-definition
```

### 管理已安装的代理

```bash
# 列出所有代理
opencode agent list

# 删除代理
opencode agent delete my-agent

# 更新代理
opencode agent update my-agent

# 查看代理详情
opencode agent show my-agent
```

## 最佳实践

1. **明确代理身份**：清晰地定义代理的角色和专业领域
2. **设置合理的温度**：创意性任务使用较高的温度，精确任务使用较低的温度
3. **配置合适的上下文**：根据代理的专业领域设置 `include_patterns` 和 `exclude_patterns`
4. **版本管理**：为代理定义文件使用版本控制
5. **团队共享**：将代理定义文件放入项目仓库，团队共享使用
6. **渐进式复杂化**：从简单的代理开始，随着需求增加逐步完善定义
7. **测试验证**：创建代理后，用几个典型场景测试其表现

## 下一步

- 了解 [PR 审核工作流](pr-review.md) 与自定义代理的结合使用
- 查看 [并行工作示例](examples/parallel-work.md)
