# AI 资讯模块 (AI-News)

> 收集、提炼、归档最新 AI 技术动态 —— 让信息不再过载，让趋势一目了然。

---

## 一、模块定位

AI 领域每天都有海量信息涌现：新论文、新产品、新开源项目、新工具版本。本模块旨在：

- **定时采集** — 从主流信息源获取 AI 领域最新动态
- **智能提炼** — 利用 AI 模型对原始信息进行摘要和结构化整理
- **存档沉淀** — 按日期归档，形成可追溯的 AI 技术发展日志

---

## 二、目录结构

```
AI-News/
├── README.md            ← 本文件，模块总览
├── news/                ← 资讯归档目录
│   ├── 2026-05-15.md    ← 示例：单日资讯简报
│   ├── 2026-05-16.md
│   └── ...
└── scripts/             ← 采集与生成脚本（可选）
    ├── collect.py       ← 资讯采集脚本
    └── generate.sh      ← 自动生成工作流
```

---

## 三、资讯文件格式

每篇资讯简报使用统一的 Markdown 格式，包含以下四个核心板块：

```markdown
# AI 资讯日报 - YYYY-MM-DD

## 🔬 热门论文
重要论文的标题、链接及一句话摘要。

## 🏢 行业动态
厂商发布、融资并购、政策法规等业界要闻。

## 📦 开源项目
值得关注的新开源项目，附仓库链接和简要说明。

## 🛠 工具更新
AI 工具/框架的新版本发布、功能变更等。
```

各板块内容按重要性排列，每一条目附带原文链接以便溯源。

---

## 四、信息源

| 类别 | 来源 | 说明 |
|------|------|------|
| **论文** | ArXiv, Papers with Code, Hugging Face Daily Papers | 最新 AI 研究论文 |
| **行业新闻** | The Verge AI, TechCrunch AI, Ars Technica AI | 全球 AI 行业动态 |
| **官方博客** | OpenAI Blog, Anthropic Blog, Google AI Blog, Meta AI | 一线厂商官方发布 |
| **开源动态** | GitHub Trending, Hugging Face Blog | 热门开源项目 |
| **中文资讯** | 机器之心、量子位、36Kr AI | 中文 AI 社区动态 |
| **社交媒体** | Twitter/X AI 社区, Reddit r/MachineLearning | 社区讨论与发现 |

---

## 五、采集与生成方案

### 方案 A：手动触发（推荐起步）
通过 Hermes Agent 的 `web_search` 工具搜索各信息源，利用模型能力进行总结提炼，手动输出为 Markdown 文件。适合初期试运行。

```bash
# 示例：用 Hermes 生成资讯简报
hermes -p "搜索今天 AI 领域的热门论文和重大新闻，按标准格式生成资讯简报"
```

### 方案 B：RSS 订阅 + 定时脚本
使用 Python 脚本订阅 RSS 源（`feedparser`），定时抓取内容后调用 AI API 进行摘要，输出为 Markdown 文件。通过 `crontab` 实现每日自动运行。

```bash
# crontab 示例：每天上午 9 点执行
0 9 * * * cd /home/young/GitProject/AI-Study/AI-News && python3 scripts/collect.py
```

### 方案 C：GitHub Actions 完全自动化
配置 GitHub Actions 定时工作流，在云端完成采集 → 摘要 → 提交的全流程。无需本地服务器，适合长期运行。

```yaml
# .github/workflows/ai-news.yml
on:
  schedule:
    - cron: '0 2 * * *'  # UTC 2:00 = 北京时间 10:00
jobs:
  collect:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: python3 scripts/collect.py
      - run: |
          git add AI-News/news/
          git commit -m "📰 AI News digest $(date +%Y-%m-%d)"
          git push
```

**建议路线：方案 A → 方案 B → 方案 C**，逐步实现完全自动化。

---

## 六、使用建议

- **查看最新资讯**：`ls AI-News/news/ | tail -5` 列出最近 5 期简报
- **搜索历史内容**：`grep -rl "关键词" AI-News/news/` 查找相关资讯
- **每日阅读**：打开对应日期的 .md 文件即可获得当日 AI 全景概览

---

## 七、更新周期

| 阶段 | 频率 | 方式 |
|------|------|------|
| 初期（手动） | 每周 2-3 次 | 手动触发 Hermes 生成 |
| 中期（脚本） | 每日 | cron 定时任务 |
| 成熟期（CI） | 每日 | GitHub Actions 自动化 |

---

*让 AI 帮你追踪 AI —— 信息的二次方。*
