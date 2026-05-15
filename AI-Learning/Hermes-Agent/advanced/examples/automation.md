# 自动化工作流示例

本文演示如何使用 Hermes Agent 的定时任务（Cron）、技能系统和委派功能构建自动化工作流。

## 场景一：每日信息简报

### 需求描述

每天早上 9:00 自动生成一份包含 AI 行业新闻、技术文章推荐和 GitHub 热门项目的信息简报，并通过 Telegram 发送给用户。

### 实现方案

#### Step 1：创建简报技能

创建 `~/.hermes/skills/daily-briefing.md`：

```markdown
---
name: daily-briefing
description: 生成每日信息简报
version: 1.0.0
---

# 每日简报技能

## 描述

生成包含以下内容的每日信息简报：
1. AI 行业头条新闻
2. 技术文章推荐
3. GitHub 热门项目

## 步骤

1. 搜索今日 AI 行业最重要的 3-5 条新闻
2. 推荐 2-3 篇高质量技术文章
3. 列出 3-5 个 GitHub 热门 AI 项目
4. 以 Markdown 格式整理为简报

## 输出格式

```markdown
# 📰 每日 AI 简报 — {{date}}

## 🔥 头条新闻
1. **[标题](链接)** — 简要描述

## 📖 技术文章
1. **[文章标题](链接)** — 文章摘要

## ⭐ GitHub 热门项目
1. **[项目名](链接)** — ⭐ stars — 项目简介

---
*由 Hermes Agent 自动生成*
```
```

#### Step 2：配置定时任务

在 `~/.hermes/config.yaml` 中添加：

```yaml
cron:
  jobs:
    - name: daily-briefing
      description: "每日 AI 行业简报"
      schedule: "0 9 * * 1-5"           # 工作日早上 9:00
      type: recurring
      prompt: "使用 daily-briefing 技能生成今日 AI 简报"
      skills:
        - daily-briefing                  # 加载简报技能
        - web-search                      # 加载搜索技能
      model: gpt-4o
      temperature: 0.5
      platform: telegram
      notification:
        on_success: true
        on_failure: true
        channel: telegram
        recipient: "{{user_id}}"
```

#### Step 3：测试执行

先手动测试任务：

```bash
hermes cron run daily-briefing
```

确认输出格式和内容满意后，启用定时执行。

---

## 场景二：GitHub 仓库监控

### 需求描述

每 4 小时检查指定 GitHub 仓库的更新，包括新的 Issue、Pull Request 和 Release，如果有重要更新则发送通知。

### 实现方案

#### 配置 MCP 服务器

```yaml
# config.yaml
mcp:
  servers:
    github:
      command: npx
      args:
        - -y
        - "@modelcontextprotocol/server-github"
      env:
        GITHUB_TOKEN: "${GITHUB_TOKEN}"
```

#### 创建监控技能

```markdown
---
name: github-monitor
description: 监控 GitHub 仓库活动
version: 1.0.0
---

# GitHub 仓库监控技能

## 监控的仓库

- NousResearch/hermes-agent
- langchain-ai/langchain
- openai/openai-cookbook

## 检查项

1. **新 Issues**：检查是否有新的 bug 报告或功能请求
2. **新 Pull Requests**：检查是否有重要的代码变更
3. **新 Releases**：检查是否有新版本发布

## 通知规则

- 新 Release：立即通知
- 新 Issue（带 bug 标签）：通知
- 新 PR（重大变更）：通知
- 普通更新：仅在简报中汇总
```

#### 配置定时任务

```yaml
cron:
  jobs:
    - name: github-monitor
      description: "监控 GitHub 仓库更新"
      schedule: "0 */4 * * *"           # 每 4 小时
      type: recurring
      prompt: "使用 github-monitor 技能检查仓库更新"
      skills:
        - github-monitor
      model: gpt-4o-mini                # 简单任务用轻量模型
      notification:
        on_change: true                  # 仅在有变更时通知
        channel: telegram
```

---

## 场景三：智能周报系统

### 需求描述

每周一上午 10:00 自动生成上周工作总结，包括完成的任务、遇到的问题和下周计划。

### 实现方案

利用 Hermes Agent 的记忆系统 + 委派功能：

```yaml
cron:
  jobs:
    - name: weekly-report
      description: "生成每周工作总结"
      schedule: "0 10 * * 1"            # 每周一 10:00
      type: recurring
      prompt: |
        先生成一份上周工作总结报告。
        
        请执行以下步骤：
        1. 从记忆系统中检索上周的重要事件和完成的任务
        2. 分析工作进展和遇到的挑战
        3. 根据历史数据推荐下周的工作重点
        4. 生成结构化的周报
        
        周报格式要求：
        - 上周完成的工作
        - 关键成就
        - 遇到的问题和解决方案
        - 下周工作计划
        - 需要的资源或支持
      model: gpt-4o
      temperature: 0.5
      skills:
        - memory-retrieval
```

---

## 场景四：数据备份与清理

### 需求描述

每天凌晨 2:00 自动执行数据备份和清理任务，确保系统健康运行。

### 实现方案

```yaml
cron:
  jobs:
    - name: system-maintenance
      description: "系统维护：备份和清理"
      schedule: "0 2 * * *"
      type: recurring
      prompt: |
        执行系统维护任务：
        
        1. 备份记忆数据
           - 将记忆数据导出到备份目录
           - 保留最近 7 天的备份
        
        2. 清理日志文件
           - 删除超过 30 天的旧日志
           - 压缩超过 7 天的日志
        
        3. 检查磁盘使用情况
           - 报告当前磁盘使用率
           - 如果使用率超过 80%，发出警告
        
        4. 生成维护报告
      model: gpt-4o-mini
      notification:
        on_failure: true                 # 仅在失败时通知
        on_warning: true                 # 磁盘使用率过高时通知
```

---

## 场景五：自动化客户支持

### 需求描述

在 Discord 服务器上设置一个自动客户支持机器人，可以回答常见问题、升级复杂问题到人工支持。

### 实现方案

#### 创建支持技能

```markdown
---
name: customer-support
description: 自动客户支持
version: 1.0.0
---

# 客户支持技能

## 支持范围

1. **常见问题**：直接从知识库中检索答案
2. **技术问题**：提供解决方案或排查步骤
3. **账户问题**：引导用户到正确的自助服务页面
4. **复杂问题**：升级到人工支持团队

## 知识库

当用户提问时：
1. 首先在知识库中搜索匹配的问题
2. 如果找到匹配，直接给出答案
3. 如果未找到，尝试推理解决方案
4. 如果无法解决，礼貌地升级到人工支持

## 升级规则

如果满足以下条件之一，升级到人工支持：
- 涉及账户安全和敏感信息
- 问题无法在知识库中找到匹配
- 用户明确要求人工支持
- 同一问题重复出现 3 次以上
```

#### 配置 Discord 网关

```yaml
platforms:
  discord:
    enabled: true
    token: "${DISCORD_BOT_TOKEN}"
    guild_id: "your-guild-id"
    allowed_channels:
      - "support"                        # 仅在 support 频道响应
    skills:
      - customer-support                 # 加载支持技能
```

---

## 进阶自动化技巧

### 条件执行

仅在特定条件下执行任务：

```yaml
cron:
  jobs:
    - name: conditional-task
      schedule: "0 * * * *"
      prompt: "检查服务器状态"
      condition: "仅当上次检查返回异常时才发送通知"
      model: gpt-4o-mini
```

### 任务链

一个任务的输出作为下一个任务的输入：

```yaml
cron:
  jobs:
    - name: collect-data
      schedule: "0 8 * * *"
      prompt: "收集今天需要处理的数据"
      
    - name: process-data
      schedule:
        after: collect-data              # 在收集完成后自动触发
        delay: 60                         # 延迟 60 秒
      prompt: "处理收集到的数据：{{collect-data.output}}"
```

### 动态调度

根据 AI 的分析结果动态调整下一次执行时间：

```yaml
cron:
  jobs:
    - name: adaptive-check
      schedule: "dynamic"                # 动态调度
      base_interval: 3600                # 基准间隔 1 小时
      prompt: |
        检查系统状态并确定下次检查的时间间隔。
        如果系统正常，将间隔增加到 2 小时。
        如果发现异常，将间隔减少到 30 分钟。
```

## 监控自动化任务

```bash
# 查看所有自动化任务状态
hermes cron list

# 查看特定任务的执行历史
hermes cron logs daily-briefing

# 暂停/恢复自动化任务
hermes cron pause daily-briefing
hermes cron resume daily-briefing

# 查看系统统计
hermes cron stats
```

## 最佳实践

1. **从简单开始**：先实现一个简单的自动化任务，再逐步增加复杂度
2. **充分测试**：使用 `hermes cron run` 手动测试后再启用定时执行
3. **设置告警**：为重要任务配置失败通知
4. **监控 Token 消耗**：自动化任务可能消耗大量 Token，注意预算
5. **渐进式复杂度**：先实现单一技能，再组合使用多个技能
6. **文档化**：记录每个自动化任务的目的和配置
7. **定期审计**：定期检查自动化任务是否仍然有效
