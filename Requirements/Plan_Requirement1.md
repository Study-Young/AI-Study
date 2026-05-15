# AI-Study 项目实施方案

## 一、项目概述

### 1.1 项目目标
在 GitHub 上创建 AI-Study 项目，提供 AI 学习与资讯两大核心模块。

### 1.2 技术栈
| 用途 | 技术方案 | 说明 |
|------|---------|------|
| 版本控制 | Git + GitHub | 代码托管 |
| 文档格式 | Markdown | 所有内容使用 .md 文件 |
| 自动化 | Cron / GitHub Actions | 定时抓取 AI 资讯 |
| 资讯采集 | RSS + web scraping | 从主流 AI 网站获取内容 |
| 翻译/总结 | AI Agent 能力（Hermes 自身） | 提炼 AI 技术动态 |

---

## 二、GitHub 仓库初始化方案

### 2.1 前置条件
| 项目 | 状态 | 操作 |
|------|------|------|
| GitHub 账号 | 待确认 | 需用户提供 GitHub 账号 |
| gh CLI | 未安装 | 需安装 GitHub CLI 或手动操作 |
| Git | 已就绪 | 本地已有 git repo |

### 2.2 步骤
1. 在 GitHub 创建名为 `AI-Study` 的仓库（Public 或 Private）
2. 本地 git remote add origin 并推送

---

## 三、AI 学习模块详细方案

### 模块结构
```
AI-Study/
├── README.md
├── AI-Learning/
│   ├── README.md                        # 学习模块总览
│   ├── Claude-Code/                     # Claude Code 独立文件夹
│   │   ├── README.md                    # 工具简介
│   │   ├── installation/                # 安装部署
│   │   │   ├── Linux.md
│   │   │   ├── macOS.md
│   │   │   └── Windows.md
│   │   ├── usage/                       # 使用方法
│   │   │   ├── quick-start.md           # 快速入门
│   │   │   ├── interactive-mode.md      # 交互模式详解
│   │   │   ├── print-mode.md            # 非交互模式详解
│   │   │   └── common-flags.md          # 常用参数参考
│   │   └── advanced/                    # 进阶功能
│   │       ├── agents.md               # 自定义子代理
│   │       ├── hooks.md                # 钩子系统
│   │       ├── mcp-integration.md      # MCP 服务器集成
│   │       ├── settings.md             # 配置与 CLAUDE.md
│   │       └── examples/               # 示例
│   │           ├── code-review.md
│   │           └── parallel-tasks.md
│   │
│   ├── Hermes-Agent/                    # Hermes Agent 独立文件夹
│   │   ├── README.md
│   │   ├── installation/
│   │   │   ├── Linux-WSL.md
│   │   │   ├── macOS.md
│   │   │   └── Windows.md
│   │   ├── usage/
│   │   │   ├── quick-start.md
│   │   │   ├── cli-reference.md         # CLI 命令参考
│   │   │   ├── slash-commands.md        # 斜杠命令
│   │   │   └── multi-platform.md        # 多平台使用
│   │   └── advanced/
│   │       ├── skills.md               # 技能系统
│   │       ├── memory.md               # 持久化记忆
│   │       ├── cron.md                 # 定时任务
│   │       ├── delegation.md           # 任务委派
│   │       ├── mcp.md                  # MCP 集成
│   │       ├── gateway.md              # 消息网关
│   │       └── examples/
│   │           ├── multi-agent.md
│   │           └── automation.md
│   │
│   └── OpenCode/                        # OpenCode 独立文件夹
│       ├── README.md
│       ├── installation/
│       │   ├── Linux.md
│       │   ├── macOS.md
│       │   └── Windows.md
│       ├── usage/
│       │   ├── quick-start.md
│       │   ├── one-shot.md              # 一次性任务
│       │   ├── interactive-tui.md       # 交互 TUI
│       │   └── configuration.md         # 配置与认证
│       └── advanced/
│           ├── custom-agents.md
│           ├── pr-review.md
│           └── examples/
│               └── parallel-work.md
│
└── AI-News/                             # AI 资讯模块
    ├── README.md
    └── news/                            # 按日期存放的资讯摘要
        └── YYYY-MM-DD.md
```

### 3.1 Claude Code 文档内容规划

**安装部署**
- 各平台安装命令
- API 认证配置（OAuth / API Key）
- `claude doctor` 健康检查
- 版本管理与更新

**使用方法**
- 交互模式（REPL / tmux）
- Print 模式（`-p` 参数详解）
- 管道输入（`cat file | claude -p`）
- 会话管理（--continue / --resume）
- 常用参数速查表

**进阶功能**
- 自定义子代理（agents 系统）
- 钩子系统（PreToolUse / PostToolUse）
- MCP 服务器集成
- 设置文件与 CLAUDE.md
- 实际示例：代码审查 / 并行任务

### 3.2 Hermes Agent 文档内容规划

**安装部署**
- 各平台安装（含 WSL 注意点）
- 配置文件（config.yaml / .env）
- 提供商配置（provider / model）
- `hermes doctor` + `hermes setup`

**使用方法**
- 交互式聊天
- CLI 命令大全
- 斜杠命令速查
- 多平台接入（Telegram / Discord / Slack）

**进阶功能**
- 技能系统（skills）
- 持久化记忆（memory）
- 定时任务（cron）
- 任务委派（delegation）
- MCP 服务器集成
- 消息网关配置
- 实际示例：多 agent 协同 / 自动化工作流

### 3.3 OpenCode 文档内容规划

**安装部署**
- 各平台安装（npm / brew）
- 认证配置（opencode auth login）
- 提供商设置

**使用方法**
- 快速入门
- 一次性任务（opencode run）
- 交互 TUI 模式
- 配置与设置

**进阶功能**
- 自定义 agents
- PR Review 工作流
- 并行工作模式
- 会话与成本管理

---

## 四、AI 资讯模块详细方案

### 4.1 信息源规划
| 目标 | 来源 | 类型 |
|------|------|------|
| AI 行业动态 | ArXiv / Papers with Code | 论文 |
| AI 技术新闻 | The Verge AI / TechCrunch AI | 新闻 |
| 开源动态 | GitHub Trending / Hugging Face Blog | 开源 |
| 大厂动态 | OpenAI / Anthropic / Google AI Blog | 官方 |
| 中文资讯 | 机器之心 / 量子位 | 中文 |

### 4.2 采集方案
- **方案A（推荐）：使用 `web_search` 工具手动触发**
  - 定期执行 `web_search` 查询主流 AI 站点
  - 利用 Hermes 自身能力进行提炼总结
  - 输出为 Markdown 文件存入 `AI-News/news/` 目录

- **方案B：RSS 订阅 + 定时任务**
  - 安装 newsboat / rss2email 等工具
  - 配置 cronjob 定时采集
  - 使用 Python 脚本解析 + Hermes 总结

- **方案C：GitHub Actions 自动化**
  - 在 GitHub Actions 中配置定时工作流
  - 使用 curl + jq 获取 API 数据
  - 自动 commit 到仓库

**建议先采用方案A，后续可升级为方案C实现完全自动化。**

### 4.3 资讯输出格式
```markdown
# AI 资讯周报 - YYYY-MM-DD

## 热门论文
- [标题](链接) - 一句话摘要

## 行业动态
- 要点1
- 要点2

## 开源项目
- [项目名](链接) - 一句话说明

## 工具更新
- 新版本 / 新功能发布
```

---

## 五、工作步骤

### Phase 1: 基础设施与 GitHub 初始化
- [ ] 确认 GitHub 账号
- [ ] 安装 gh CLI 或手动创建仓库
- [ ] 初始化远程仓库并推送
- [ ] 编写项目根目录 README.md

### Phase 2: AI-Learning 模块 —— Claude Code
- [ ] 创建 Claude-Code 目录结构
- [ ] 编写安装部署文档（Linux / macOS / Windows）
- [ ] 编写使用方法文档
- [ ] 编写进阶功能与示例
- [ ] 自测：每篇文档结构完整、链接正确

### Phase 3: AI-Learning 模块 —— Hermes Agent
- [ ] 创建 Hermes-Agent 目录结构
- [ ] 编写安装部署文档
- [ ] 编写使用方法文档
- [ ] 编写进阶功能与示例
- [ ] 自测

### Phase 4: AI-Learning 模块 —— OpenCode
- [ ] 创建 OpenCode 目录结构
- [ ] 编写安装部署文档
- [ ] 编写使用方法文档
- [ ] 编写进阶功能与示例
- [ ] 自测

### Phase 5: AI-News 资讯模块
- [ ] 创建 AI-News 目录结构
- [ ] 编写采集脚本（Python）
- [ ] 创建第一期 AI 资讯摘录作为模板
- [ ] 配置定时任务（cronjob）
- [ ] 自测

### Phase 6: 最终整合与验证
- [ ] 验证 GitHub 仓库结构与文档完整性
- [ ] 验证所有链接可访问
- [ ] 提交最终 commit 并推送

---

## 六、自我验证标准

| 检查项 | 标准 |
|--------|------|
| 目录结构 | 与设计方案完全一致 |
| 文件完整性 | 无遗漏文件，所有 .md 有内容 |
| 文档可读性 | Markdown 格式正确、有标题层级 |
| 链接有效性 | 内部交叉引用和外部链接均有效 |
| GitHub 同步 | 本地与远程一致 |
| 资讯模块 | 至少生成一期示例内容 |

---

> **备注：**
> - `openclaw` 已确认为 **OpenCode (OpenAI Codex CLI)**
> - 当前目录已在 git repo 中（未 commit）
> - gh CLI 未安装，需要手动处理或安装后操作
> - Hermes Agent 已在使用中，文档可参考实际使用经验
