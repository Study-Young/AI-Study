# 记忆系统

记忆系统（Memory System）是 Hermes Agent 的关键功能，允许 AI 在不同会话之间保持持久记忆。借助记忆系统，AI 可以记住用户的偏好、历史对话中的重要信息，以及长期项目中的上下文。

## 工作原理

记忆系统通过以下机制工作：

1. **提取**：在对话过程中提取重要信息
2. **存储**：将提取的信息持久化到存储后端
3. **检索**：在新会话开始时或需要时检索相关记忆
4. **更新**：根据新的对话内容更新现有记忆
5. **遗忘**：自动或手动删除不再需要的记忆

## 记忆类型

### 用户记忆

关于用户的信息：

```yaml
- type: user_preference
  content: "用户偏好 Python 进行数据分析"
  timestamp: "2026-05-15T10:30:00Z"
  importance: medium

- type: user_info
  content: "用户是一名软件工程师，擅长后端开发"
  timestamp: "2026-05-14T15:20:00Z"
  importance: high
```

### 会话记忆

关于历史对话的信息：

```yaml
- type: conversation_summary
  content: "讨论了微服务架构的设计模式"
  timestamp: "2026-05-13T09:00:00Z"
  importance: low
```

### 项目记忆

关于长期项目的信息：

```yaml
- type: project_context
  content: "项目名称为 AI-Study，目标是学习 AI 开发"
  tags: ["project", "AI-Study"]
  timestamp: "2026-05-01T08:00:00Z"
  importance: high
```

### 任务记忆

关于待办任务和进度：

```yaml
- type: task
  content: "需要完成 Hermes Agent 文档的中文翻译"
  status: in_progress
  deadline: "2026-05-20"
  importance: medium
```

## 存储后端

Hermes Agent 支持多种存储后端：

### JSON 文件（默认）

最简单的后端，记忆存储在 JSON 文件中：

```yaml
# config.yaml
memory:
  backend: json
  path: ~/.hermes/memory.json
```

优点：简单、便携、易于检查
缺点：不适合大规模使用

### SQLite

适合单用户的中等规模使用：

```yaml
memory:
  backend: sqlite
  path: ~/.hermes/memory.db
```

优点：性能好、查询灵活、事务支持
缺点：需要 SQLite 支持

### PostgreSQL

适合多用户或服务器部署：

```yaml
memory:
  backend: postgres
  dsn: "postgresql://user:password@localhost:5432/hermes_memory"
  table: memories
```

优点：高并发、远程访问、持久化保障
缺点：需要 PostgreSQL 服务器

### 向量数据库

支持语义搜索的记忆后端：

```yaml
memory:
  backend: qdrant
  host: localhost
  port: 6333
  collection: hermes_memories
  embedding_model: text-embedding-3-small
```

支持的向量数据库：Qdrant、Chroma、Pinecone、Weaviate

优点：语义搜索、相似度检索
缺点：需要向量数据库服务

## 记忆的自动管理

### 重要性评分

每条记忆都有一个重要性评分，影响其保留优先级：

```yaml
# 配置重要性阈值
memory:
  importance_threshold: 0.3       # 低于此值可能被自动清理
  retention_policy:               # 保留策略
    high: forever                  # 高重要性永不删除
    medium: 90d                    # 中重要性保留 90 天
    low: 30d                       # 低重要性保留 30 天
```

### 自动摘要

当记忆数量过多时，系统会自动压缩和摘要：

```yaml
memory:
  auto_summarize: true
  summarize_threshold: 100        # 超过 100 条时触发摘要
  summarize_model: gpt-4o-mini    # 用于摘要的模型
```

### 记忆合并

相似或相关的记忆会自动合并：

```yaml
memory:
  auto_merge: true
  merge_similarity: 0.8          # 相似度高于此值则合并
```

## CLI 命令

```bash
# 查看当前记忆
hermes memory show

# 搜索记忆
hermes memory search "python"

# 清除记忆
hermes memory clear

# 导出记忆
hermes memory export backup.json

# 导入记忆
hermes memory import backup.json

# 统计记忆
hermes memory stats
```

## 聊天命令

```text
/memory                         # 显示当前记忆
/memory clear                   # 清除所有记忆
/memory search <关键词>          # 搜索记忆
/memory add <内容>              # 添加新记忆
/memory remove <id>             # 删除指定记忆
/remember                       # 记住当前对话的重点
```

## 记忆配置完整示例

```yaml
# ~/.hermes/config.yaml
memory:
  enabled: true
  backend: sqlite               # json | sqlite | postgres | qdrant | chroma
  path: ~/.hermes/memory.db     # 本地文件路径（JSON/SQLite）
  
  # 向量嵌入配置
  embedding:
    provider: openai            # 嵌入模型提供商
    model: text-embedding-3-small
    dimensions: 1536
  
  # 检索配置
  retrieval:
    max_results: 10             # 每次检索最大记忆数
    min_score: 0.5              # 最低相似度分数
    hybrid_search: true         # 启用混合搜索（关键词+语义）
  
  # 管理策略
  management:
    auto_extract: true          # 自动从对话中提取记忆
    auto_summarize: true        # 自动摘要
    auto_merge: true            # 自动合并相似记忆
    importance_threshold: 0.3
  
  # 保留策略
  retention:
    high: forever
    medium: 90d
    low: 30d
```

## 隐私与安全

- **本地存储优先**：默认所有记忆存储在本地
- **加密选项**：支持加密存储敏感记忆
- **选择性记忆**：用户可以控制哪些信息被记住
- **可遗忘权**：用户可以随时清除全部或部分记忆
- **导出功能**：用户可以随时导出自己的记忆数据

## 最佳实践

1. **定期清理**：定期检查和清理不需要的记忆
2. **合理配置后端**：根据使用场景选择合适的存储后端
3. **设置重要性阈值**：避免存储过多低价值信息
4. **启用自动摘要**：减少记忆碎片化
5. **注意隐私**：不在记忆中存储密码等敏感信息
