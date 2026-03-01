# AI Daily Digest

## 项目概述

全自动每日技术内容聚合系统。从 90+ 技术博客（来源于 HN Popularity Contest 2025）、25+ X/Twitter 账号、Hacker News、Reddit、Product Hunt、Lobste.rs 抓取内容，使用 AI 进行评分、分类、摘要。同时聚合 ClawFeed 日报和 GitHub Trending 热门项目。生成 Markdown 格式的每日精选报告，自动推送到 GitHub 和微信。

## 技术栈

- **运行时**: Bun（TypeScript，零 npm 依赖）
- **主 AI**: Google Gemini 2.0 Flash
- **备用 AI**: DeepSeek（OpenAI 兼容 API）
- **X/Twitter 代理**: RSSHub（CI 中以 Docker 容器运行）
- **自动化**: GitHub Actions（每日 UTC 23:39 / 北京时间 07:39）
- **通知推送**: Server酱（微信推送）

## 项目结构

```
scripts/digest.ts     # 主程序（单文件，约 1400 行）
digests/              # 生成的每日精选 Markdown 文件
.github/workflows/    # GitHub Actions 工作流
```

## 运行命令

```bash
# 本地运行（默认：48 小时范围，Top 15，中文）
GEMINI_API_KEY=xxx bun scripts/digest.ts

# 自定义参数
bun scripts/digest.ts --hours 24 --top-n 15 --lang zh --output digest.md

# CLI 参数：--hours <n>, --top-n <n>, --lang zh|en, --output <path>, --help
```

## 环境变量

| 变量 | 必填 | 说明 |
|------|------|------|
| `GEMINI_API_KEY` | 是 | Google Gemini API 密钥 |
| `OPENAI_API_KEY` | 否 | DeepSeek 备用密钥 |
| `OPENAI_API_BASE` | 否 | 备用 API 端点 |
| `OPENAI_MODEL` | 否 | 备用模型名称 |
| `RSSHUB_BASE_URL` | 否 | RSSHub 代理地址（默认：https://rsshub.app） |
| `X_ACCOUNTS` | 否 | 逗号分隔的 Twitter 账号列表 |
| `SERVERCHAN_KEY` | 否 | Server酱密钥（微信推送） |

## 架构与核心模式

### 单文件设计
所有逻辑集中在 `scripts/digest.ts` —— RSS 解析、AI 评分、摘要生成、报告渲染、CLI 处理。无外部依赖，使用 Bun 内置 API 和原生 `fetch()`。

### 数据流
1. **并行抓取** — 三路并行：RSS Feeds（10 并发）、ClawFeed 日报、GitHub Trending
2. **过滤** — 去重 + 按时间范围筛选（仅 RSS 文章）
3. **评分** — Gemini AI 分批评分（每批 10 篇，三维度各 1-10 分）
4. **摘要** — 为 Top-N 文章生成中文/英文摘要
5. **报告** — 渲染 Markdown，含 ClawFeed 章节、GitHub Trending 表格、Mermaid 图表、分类分组

### 数据源
- **RSS Feeds（96 个）** — 90 个技术博客 + HN Best + Reddit (3) + Product Hunt + Lobste.rs
- **X/Twitter** — 25+ 账号通过 RSSHub 代理
- **ClawFeed** — AI 驱动的多源新闻聚合日报（独立章节，不参与评分）
- **GitHub Trending** — 每日热门开源项目，全语言 + Python（独立章节，不参与评分）

### 容错机制
- Gemini 429 限流自动重试，指数退避（3 次重试：30s、60s、90s）
- Gemini 不可用时自动切换到 DeepSeek
- `Promise.allSettled` 优雅处理 RSS 拉取失败
- AI 评分失败时使用默认分数（5/10）

### 性能参数
```
FEED_FETCH_TIMEOUT_MS = 15_000   # 每个 Feed 15 秒超时
FEED_CONCURRENCY = 10            # 并行拉取 10 个 Feed
GEMINI_BATCH_SIZE = 10           # 每批 10 篇文章
MAX_CONCURRENT_GEMINI = 2        # 最多 2 个并发 AI 批次
```

### 报告结构
1. `📰 AI 博客每日精选` — 标题
2. `📝 今日看点` — AI 生成的趋势总结
3. `🌐 ClawFeed 日报精选` — ClawFeed 日报内容（头条、Top 10、推荐关注、今日观察）
4. `🔥 GitHub Trending` — 今日热门开源项目表格（15 个，AI 相关标 🤖）
5. `🏆 今日必读` — 全源 Top 5 文章深度展示
6. `📊 数据概览` — 统计数据 + Mermaid 图表
7. 分类文章列表 — 按分类分组展示所有精选文章
8. 页脚

### 分类体系
`ai-ml` | `security` | `engineering` | `tools` | `opinion` | `other`

### 文章评分
三个维度（各 1-10 分），总分 30 分：
- **相关性** — 对技术社区的价值
- **质量** — 写作深度与质量
- **时效性** — 当前话题相关度

## 开发约定

- RSS 解析使用正则（无 XML 库）—— 同时支持 Atom 和 RSS 格式
- AI Prompt 返回结构化 JSON —— 解析失败时必须有兜底逻辑
- 精选文件命名：`digest-YYYYMMDD.md`
- 提交信息格式：`📰 Daily digest YYYY-MM-DD`
- 输出语言默认中文（`zh`），可通过 `--lang en` 切换英文
- 所有 HTTP 调用使用原生 `fetch()`，不使用 axios/node-fetch

## GitHub Actions 配置

**必填 Secrets**: `GEMINI_API_KEY`
**可选 Secrets**: `DEEPSEEK_API_KEY`, `X_AUTH_TOKEN`, `X_CT0`, `SERVERCHAN_KEY`
**可选 Variables**: `X_ACCOUNTS`
