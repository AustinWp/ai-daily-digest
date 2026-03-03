# AI Daily Digest

每天自动从 **104 个技术博客**、**30 个 X/Twitter 账号**、**Hacker News**、**7 个 Reddit 子版块**、**Product Hunt**、**Lobste.rs** 抓取最新内容，融合 **ClawFeed 日报** 和 **GitHub Trending** 热门项目，通过 **Gemini AI** 智能评分、分类和摘要，生成结构化的中文每日技术精选日报，并自动推送到微信。

## 它能做什么

- **多源聚合** — 订阅 114 个 RSS 源（技术博客 + HN + Reddit + Product Hunt + Lobste.rs）加上 30 个 X/Twitter 技术账号
- **ClawFeed 日报** — 聚合 AI 驱动的多源新闻日报，包含头条、精选 Top 10、推荐关注
- **GitHub Trending** — 每日热门开源项目（全语言 + Python），AI 相关项目自动标记
- **AI 智能评分** — 使用 Gemini AI 从相关性、质量、时效性三个维度（各 1-10 分）对每篇文章打分
- **自动分类** — 将文章归入 AI/ML、安全、工程、工具/开源、观点/讨论、其他 六大分类
- **中文摘要** — 为每篇英文文章生成中文标题、4-6 句摘要和推荐理由
- **趋势洞察** — AI 生成当日宏观技术趋势总结
- **可视化统计** — 包含分类分布饼图、关键词频率图表
- **微信推送** — 通过 Server酱 自动将精选内容推送到微信
- **全自动化** — GitHub Actions 定时运行，零人工干预

## 工作流程

```
RSS Feeds (114个源)    ──┐
X/Twitter (30个账号)   ──┤
                         ├──→ 抓取文章 → 时间过滤 → AI 评分 → 选取 Top N → AI 摘要 ──┐
ClawFeed 日报          ──┤                                                              ├──→ 生成日报 → 推送
GitHub Trending        ──┘                                                              ┘
```

**详细流程：**

1. **并行抓取** — 三路并行：RSS/X 源获取文章、ClawFeed API 获取日报、GitHub Trending 页面解析
2. **过滤** — 筛选最近 24 小时内发布的文章（RSS 部分）
3. **评分** — Gemini AI 分批评估（10 篇/批），三维度打分 + 自动分类 + 关键词提取
4. **精选** — 按总分排序，选取 Top 15
5. **摘要** — 为精选文章生成中文标题、摘要和推荐理由，并生成当日趋势分析
6. **输出** — 渲染为结构化 Markdown 日报，保存到 `digests/` 目录
7. **推送** — 自动提交到 GitHub 并推送摘要到微信

## 日报结构

每份日报包含：

- **信源说明 + 栏目导航** — 头部展示信源概览和本期内容目录
- **今日看点** — 3-5 句宏观趋势总结
- **今日必读 Top 5** — 全源最高分文章详细展示（含摘要、推荐理由）
- **ClawFeed 日报精选** — 头条新闻、精选 Top 10、推荐关注、今日观察
- **GitHub Trending** — 今日热门开源项目表格（15 个，AI 项目标 🤖）
- **分类文章列表** — 按六大分类展示所有精选文章
- **Design & Generative AI** — 设计与生成式 AI 专题（生成式 UI / 图片 / 世界模型 / 视频）
- **数据概览** — 来源数、文章数统计表 + 可视化图表

> 查看历史日报：[`digests/`](./digests/) 目录

## 数据源

### RSS Feeds（108 个）

**96 个技术博客** — 来自 [HN Popularity Contest 2025](https://hnblogs.substack.com/) 精选

<details>
<summary>展开完整博客列表</summary>

simonwillison.net · jeffgeerling.com · seangoedecke.com · krebsonsecurity.com · daringfireball.net · ericmigi.com · antirez.com · idiallo.com · maurycyz.com · pluralistic.net · shkspr.mobi · lcamtuf.substack.com · mitchellh.com · dynomight.net · utcc.utoronto.ca/~cks · xeiaso.net · devblogs.microsoft.com/oldnewthing · righto.com · lucumr.pocoo.org · skyfall.dev · garymarcus.substack.com · rachelbythebay.com · overreacted.io · timsh.org · johndcook.com · gilesthomas.com · matklad.github.io · derekthompson.org · evanhahn.com · terriblesoftware.org · rakhim.exotext.com · joanwestenberg.com · xania.org · micahflee.com · nesbitt.io · construction-physics.com · tedium.co · susam.net · entropicthoughts.com · buttondown.com/hillelwayne · dwarkesh.com · borretti.me · wheresyoured.at · jayd.ml · minimaxir.com · geohot.github.io · paulgraham.com · filfre.net · blog.jim-nielsen.com · dfarq.homeip.net · jyn.dev · geoffreylitt.com · downtowndougbrown.com · brutecat.com · eli.thegreenplace.net · abortretry.fail · fabiensanglard.net · oldvcr.blogspot.com · bogdanthegeek.github.io · hugotunius.se · gwern.net · berthub.eu · chadnauseam.com · simone.org · it-notes.dragas.net · beej.us · hey.paris · danielwirtz.com · matduggan.com · refactoringenglish.com · worksonmymachine.substack.com · philiplaine.com · steveblank.com · bernsteinbear.com · danieldelaney.net · troyhunt.com · herman.bearblog.dev · tomrenner.com · blog.pixelmelt.dev · martinalderson.com · danielchasehooper.com · chiark.greenend.org.uk/~sgtatham · grantslatton.com · experimental-history.com · anildash.com · aresluna.org · michael.stapelberg.ch · miguelgrinberg.com · keygen.sh · mjg59.dreamwidth.org · computer.rip · tedunangst.com

</details>

**8 个设计与生成式 AI 博客**

| 博客 | 侧重 |
|------|------|
| Hugging Face Blog | 开源模型首发、Diffusers 库 |
| Lilian Weng (OpenAI) | Diffusion / World Model 技术综述 |
| The Decoder | 生成式 AI 专业新闻 |
| Replicate Blog | 模型部署、评测 |
| NVIDIA Technical Blog | NeRF、3D Gaussian、视频生成 |
| Stability AI Blog | Stable Diffusion 一手发布 |
| Google DeepMind Blog | Imagen、Veo、Genie |
| Runway Research | Gen-2/Gen-3 研究论文 |

**聚合平台**

| 平台 | 说明 |
|------|------|
| Hacker News Best | HN 精选 |
| Product Hunt | 每日热门产品 |
| Lobste.rs | 高质量技术社区 |

**7 个 Reddit 子版块**

| Sub | 方向 |
|-----|------|
| r/programming | 通用编程 |
| r/MachineLearning | 机器学习学术 |
| r/LocalLLaMA | 本地 LLM |
| r/StableDiffusion | AI 图片生成 |
| r/midjourney | Midjourney |
| r/comfyui | ComfyUI 工作流 |
| r/singularity | AI 前沿讨论 |

### X/Twitter（30 个账号，通过 RSSHub 代理）

| 账号 | 方向 |
|------|------|
| @grok · @xingpt · @OpenAI · @AnthropicAI · @deepseek_ai · @LangChain | AI 产品 & 平台 |
| @_akhaliq · @DrJimFan · @hardmaru · @huggingface · @LumaLabsAI | AI 研究 & 生成式 AI |
| @dotey · @Khazix0918 · @FinanceYF5 · @EXM7777 | 中文 AI 资讯 |
| @sodawhite_dev · @yetone · @runes_leo · @abskoop · @egeberkina · @vasuman · @eptwts · @steipete · @rileybrown · @gregisenberg · @oran_ge | 开发者 & 独立开发 |
| @yangyi · @manateelazycat · @lifesinger · @yuxiyou | 中文技术社区 |

### 独立数据源（不参与评分）

| 数据源 | 说明 |
|--------|------|
| ClawFeed 日报 | AI 驱动的多源新闻聚合 |
| GitHub Trending | 全语言 + Python 热门项目 |

**合计：114 个 RSS + 30 个 X/Twitter + ClawFeed + GitHub Trending = 146 信息源**

## 技术栈

| 组件 | 技术 |
|------|------|
| 运行时 | [Bun](https://bun.sh/) |
| 语言 | TypeScript（单文件，零 npm 依赖） |
| AI 评分 | Google Gemini 2.0 Flash |
| AI 备选 | DeepSeek（OpenAI 兼容接口） |
| X/Twitter 代理 | [RSSHub](https://docs.rsshub.app/)（Docker） |
| 自动化 | GitHub Actions |
| 微信推送 | [Server酱](https://sct.ftqq.com/) |

## 快速开始

### 本地运行

```bash
# 安装 Bun（如未安装）
curl -fsSL https://bun.sh/install | bash

# 运行
GEMINI_API_KEY=your-key bun scripts/digest.ts \
  --hours 24 \
  --top-n 15 \
  --lang zh \
  --output digest.md
```

### 命令行参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `--hours <n>` | 抓取最近 N 小时的文章 | `48` |
| `--top-n <n>` | 精选文章数量 | `15` |
| `--lang <lang>` | 摘要语言（`zh` / `en`） | `zh` |
| `--output <path>` | 输出文件路径 | `./digest-YYYYMMDD.md` |

### GitHub Actions 自动部署

Fork 本仓库后，在 **Settings → Secrets and variables → Actions** 中配置：

**Secrets（必需）：**

| Secret | 说明 |
|--------|------|
| `GEMINI_API_KEY` | Gemini API Key（[免费获取](https://aistudio.google.com/apikey)） |

**Secrets（可选）：**

| Secret | 说明 |
|--------|------|
| `DEEPSEEK_API_KEY` | DeepSeek API Key，作为 Gemini 的备用方案 |
| `X_AUTH_TOKEN` | X/Twitter 认证 Token，用于抓取 X 动态 |
| `X_CT0` | X/Twitter CSRF Token |
| `SERVERCHAN_KEY` | Server酱 Key，用于微信推送 |

**Variables（可选）：**

| Variable | 说明 |
|----------|------|
| `X_ACCOUNTS` | 要关注的 X 账号列表（逗号分隔） |

配置完成后，GitHub Actions 会在每天**北京时间 7:39** 自动运行，也可在 Actions 页面手动触发。

## 日报存放

所有日报保存在 `digests/` 目录下，文件名格式 `digest-YYYYMMDD.md`。

## 自定义

### 修改 RSS 源

编辑 `scripts/digest.ts` 中的 `RSS_FEEDS` 数组，添加或移除 RSS 源。

### 修改 X 账号

在 GitHub Actions Variables 中修改 `X_ACCOUNTS`，或在本地运行时设置环境变量：

```bash
X_ACCOUNTS=account1,account2,account3
```

### 修改运行时间

编辑 `.github/workflows/daily-digest.yml` 中的 cron 表达式。

## License

MIT
