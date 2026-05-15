# 技能系统

技能（Skills）是 Hermes Agent 的核心功能之一。技能是以 Markdown 格式编写的可复用程序，能够让 AI 学会执行特定任务、使用外部工具和遵循特定流程。

## 技能的工作原理

技能本质上是一组结构化的指令，通过 Markdown 文件定义。当技能被加载时，其内容会被注入到 AI 的系统提示词中，使 AI 了解如何执行特定任务。

技能可以：
- 定义执行特定任务的步骤
- 集成外部工具和 API
- 设置输出格式规范
- 定义验证规则
- 链式调用其他技能

## 技能目录结构

所有技能存储在 `~/.hermes/skills/` 目录下，每个技能是一个独立的 Markdown 文件：

```
~/.hermes/skills/
├── web-search.md        # 网络搜索技能
├── code-review.md       # 代码审查技能
├── data-analysis.md     # 数据分析技能
└── my-custom-skill.md   # 自定义技能
```

## 创建第一个技能

创建一个名为 `greet.md` 的技能文件：

```markdown
---
name: greet
description: 用多种语言问候用户
version: 1.0.0
author: 你的名字
---

# 问候技能

当用户要求你问候时，按照以下步骤执行：

## 步骤

1. 检测用户的输入语言
2. 根据检测到的语言选择对应的问候语
3. 输出格式化的问候

## 问候语映射

- 中文：你好！今天过得怎么样？
- 英语：Hello! How are you today?
- 日语：こんにちは！お元気ですか？
- 韩语：안녕하세요! 오늘은 어떠세요?
- 法语：Bonjour ! Comment allez-vous ?

## 示例

用户说：用日语问候我
AI 应回答：こんにちは！お元気ですか？
```

## 技能文件格式

### 元数据头（Frontmatter）

每个技能文件以 YAML frontmatter 开头：

```yaml
---
name: skill-name           # 技能的唯一名称
description: 简短描述     # 一句话描述技能功能
version: 1.0.0            # 版本号（语义化版本）
author: 作者名            # 技能作者
dependencies: []          # 依赖的其他技能
tags: [tag1, tag2]        # 标签，用于分类
icon: 🔧                  # 显示图标（可选）
---
```

### 内容结构

技能内容使用 Markdown 格式，推荐的结构包括：

1. **标题**：技能的名称和用途
2. **前提条件**：使用技能前需要满足的条件
3. **步骤**：详细的操作步骤，使用编号列表
4. **规则**：AI 必须遵守的行为规则
5. **示例**：输入/输出示例，帮助 AI 理解预期行为
6. **错误处理**：异常情况的处理方法

## 示例技能

### 网络搜索技能

```markdown
---
name: web-search
description: 通过搜索引擎查询互联网信息
version: 1.1.0
dependencies: []
tags: [search, internet]
---

# 网络搜索技能

当用户需要查询实时信息时，使用此技能进行网络搜索。

## 工具

使用 `search_web(query)` 函数执行搜索。

## 步骤

1. 分析用户的问题，提取关键搜索词
2. 调用 `search_web()` 函数执行搜索
3. 分析搜索结果，提取相关信息
4. 用中文整理并呈现搜索结果

## 规则

- 搜索词应该简洁、具体
- 对于复杂查询，分解为多个搜索词
- 始终注明信息来源
- 如果搜索无结果，尝试使用不同的搜索词
```

### 代码审查技能

```markdown
---
name: code-review
description: 审查代码质量，发现潜在问题
version: 1.0.0
tags: [code, development]
---

# 代码审查技能

审查用户提交的代码，从以下维度进行分析：

## 审查维度

1. **正确性**：代码逻辑是否正确
2. **安全性**：是否存在安全漏洞
3. **性能**：是否存在性能问题
4. **可读性**：代码是否易于理解
5. **最佳实践**：是否符合语言/框架的最佳实践

## 输出格式

按照以下格式输出审查结果：

### 问题 1：[严重程度]
- **文件**：[文件名]
- **行号**：[行号]
- **问题描述**：[详细描述]
- **建议修复**：[修复方案]
```

## 安装和管理技能

### CLI 命令

```bash
# 列出已安装的技能
hermes skills list

# 安装技能（从仓库）
hermes skills install web-search

# 安装技能（从本地文件）
hermes skills install /path/to/skill.md

# 创建新技能
hermes skills create my-skill

# 移除技能
hermes skills remove my-skill

# 查看技能详情
hermes skills info web-search
```

### 在配置中启用技能

```yaml
# ~/.hermes/config.yaml
skills:
  auto_load:
    - web-search
    - code-review
  default_enabled:
    - web-search
```

### 在聊天中使用

```text
/skills                        # 列出已加载的技能
/skills enable web-search      # 启用技能
/skills disable web-search     # 禁用技能
```

## 技能模板

使用 `hermes skills create` 命令时会生成模板：

```markdown
---
name: my-skill
description: 在这里填写技能描述
version: 1.0.0
author: your-name
---

# 技能名称

## 描述

简要描述这个技能的用途。

## 使用步骤

1. 步骤一
2. 步骤二
3. 步骤三

## 规则

- 规则一
- 规则二

## 示例

用户输入：
AI 输出：
```

## 最佳实践

1. **单一职责**：每个技能只做一件事，做得好
2. **明确步骤**：步骤要清晰、可执行，避免歧义
3. **提供示例**：好示例比好描述更有用
4. **版本管理**：使用语义化版本号追踪变更
5. **依赖管理**：明确声明依赖的其他技能
6. **测试**：创建测试用例验证技能行为
7. **文档**：在技能内提供充分的使用说明
8. **安全**：不要在技能中硬编码敏感信息

## 技能市场

社区贡献的技能可以从 Hermes Agent 技能仓库安装：

```bash
# 浏览社区技能
hermes skills search analysis

# 安装社区技能
hermes skills install community/web-search
```

你也可以将自己的技能发布到社区仓库分享给其他人。
