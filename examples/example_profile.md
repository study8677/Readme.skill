# <Redacted> · AI-Native Developer Profile
> 基于 **107** 天的本地 Claude Code + Codex 对话数据自动生成 · 2026-04-17
> _个人理念：把复杂问题拆到不可再拆的基本事实或约束，只从这些"不可否认的底层事实"出发进行推理，而不依赖经验、类比或既有结论。_

---

## 一览

- 在 **107** 天里完成 **277** 次 Claude sessions + **390** 次 Codex threads，共 **115,048** 条 Claude 消息 + **1,756** 条 Codex prompts
- 日均产出：**10.4** commits / **5,948** 行代码变动 / **11.7** GitHub contributions
- 同时维护 **13** 个仓库，横跨 **5+** 门语言（Python / TypeScript / Rust / Go / HTML）
- 同期 GitHub：**651** commits / **51** PRs / **29** issues / **798** 总贡献
- 主力工具：Claude Code (Opus 4.6 + Sonnet 4.6) + Codex CLI (GPT-5.4)

## 🚀 Velocity & Leverage — AI 让一个人拥有了小团队的交付能力

> 13 个仓库、5 门语言、日均 10 commit、40 万行代码 churn —— 这种跨栈广度和交付密度，只有 AI 协作才现实。

| 指标 | 数值 | 说明 |
| --- | ---: | --- |
| 日均 commits | 10.4 | 709 local commits / 68 活跃天 |
| 日均代码变动 | 5,948 行 | 404,497 LOC churn / 68 天 |
| 日均 GitHub contributions | 11.7 | 798 contributions / 68 天 |
| 同时维护仓库 | 13 个 | 过去一年有 commit 的仓库数 |
| 跨栈语言 | 5+ 门 | Python · TypeScript · Rust · Go · HTML/JS |
| GitHub 贡献爆发 | 04-02 → 04-05 | 连续 4 天 daily > 20（52 / 66 / 33 / 20） |
| 开源影响力 | **1,317** stars | 跨 29 个被 star 的仓库 |

## 🤖 AI-Native 实践

> 不是「偶尔问问 AI」，是把多 LLM 编排、planning、structured workflows 都跑通。

### 多模型编排

| 模型 | sessions / threads | spent tokens | cache-read | 用途倾向 |
| --- | ---: | ---: | ---: | --- |
| Claude Opus 4.6 | 主力 | 135,018,198 | 3,399,640,688 | 深度推理、复杂规划、plan-mode |
| Claude Sonnet 4.6 | 偶尔批量 | 2,644,538 | 124,109,847 | 快速迭代、批量任务 |
| GPT-5.4 (Codex) | 253 threads | 3,255,874,561 | — | 第二意见、跨工具诊断 |
| GPT-5.4-mini | 13 threads | 264,066,614 | — | 轻量任务 |
| Codex(未标注) | 119 threads | 767,350,122 | — | 早期 / 模型字段空 |

### 高级能力深度使用

- **Plan-mode**: **88** 次
- **Effort 调节**: **522** 次 `/effort`
- **Skills**: 共 **18** 个（Claude 11 + Codex 7）
- **Plans**: **55** 份；Tasks: **51** 个
- **Hooks**: **1** 个；Automations: **1** 个；Rules: **1** 个

### Prompt caching 熟练度

每花费 1 个新 token，复用 **25.6** 个缓存 token（cache-read 占总 IO 的 **96%**）。

### Reasoning effort 偏好

| effort | 次数 | 占比 |
| --- | ---: | ---: |
| xhigh | 199 | **51.0%** |
| unspecified | 120 | 30.8% |
| high | 46 | 11.8% |
| medium | 22 | 5.6% |
| low | 3 | 0.8% |

## 🔧 AI 基础设施 — 不只用 AI，还在给 AI 造工具

> 从 skill 到 hook 到 automation，我在构建让 AI 更高效地帮我工作的元层基础设施。

### 自建 Skills（原创工作流）

| 名称 | 描述 | 调用次数 | 工具 |
| --- | --- | ---: | --- |
| deploy | 全周期部署：commit → push → SSH → pull → build → atomic deploy | — | Claude |
| ops-report | 查询生产数据库，生成日运营报表（DAU / 收入 / API 成本 / 留存） | — | Claude |
| ssh-prod | 通过 TAT 远程执行生产服务器命令 | 10 | Claude |
| integrator-patrol | 集成健康巡检：按协作协议做 full smoke check | — | Claude |
| log-patrol | 扫描所有服务模块日志，输出结构化错误报告 | — | Claude |
| self-improving | 自反思 + 自批评 + 自学习 + 自组织记忆 | — | Claude |
| create-url-watch-automation | 创建定时 URL 监控自动化（飞书 + GitHub Issues） | — | Codex |

### 共享 Skills（跨工具复用）

| 名称 | 描述 | 工具 |
| --- | --- | --- |
| docx | Word 文档创建 / 读取 / 编辑 | Claude + Codex |
| pdf | PDF 全生命周期（创建 / 合并 / 拆分 / 水印 / OCR） | Claude + Codex |
| pdf-reading | PDF 内容提取与视觉检查 | Claude + Codex |
| pptx | PPT 创建 / 编辑 / 解析 | Claude + Codex |
| xlsx | 电子表格处理（Excel / CSV / TSV） | Claude + Codex |

### 其他基础设施

- **Claude Hook**: Glass 通知 — 当 AI 等待用户输入时弹出系统通知
- **Codex Automation**: bug-monitor — 自动化 bug 监控
- **Codex Rule**: default.rules — 自定义行为规则

## 🛠️ AI 协作风格

### 最常用的 slash 命令 Top 12

| # | 命令 | 次数 | 含义 |
| --- | --- | ---: | --- |
| 1 | `/effort` | 522 | 切换推理深度 |
| 2 | `/usage` | 121 | 检查 token 用量 |
| 3 | `/plan` | 88 | 进入 plan-mode |
| 4 | `/btw` | 59 | 后台轨迹切换 |
| 5 | `/resume` | 52 | 恢复历史 session |
| 6 | `/clear` | 51 | 清空对话上下文 |
| 7 | `/compact` | 25 | 压缩上下文 |
| 8 | `/vibe-forge` | 20 | 自研 skill 分发 |
| 9 | `/init` | 13 | 初始化 CLAUDE.md |
| 10 | `/ssh-prod` | 10 | 生产服务器巡检 |
| 11 | `/model` | 9 | 切换模型 |
| 12 | `/skill-creator` | 8 | 创建 / 编辑 skill |

### Session 架构

典型流程：直接进入任务 → 深度迭代 → 遇到上下文膨胀时 `/compact` 或 `/clear` 回收 → 继续交付。复杂任务中插入 `/plan` 做阶段性规划。

- **16.7%** 的 session 主动使用 `/compact` 或 `/clear`（67 / 401 —— 有意识地管理上下文窗口）
- **18.8%** 的 session 通过 `/resume` 从历史恢复（52 / 277 —— 近五分之一的工作是接续前次深度协作）
- **平均会话深度**: **415.3** 条消息 / session —— 倾向长链路深度协作
- **最长 session**: **172.9 小时** / **1,444** 条消息（连续多日的复杂任务）

## 📂 项目与领域分布

跨 **25+** 个项目活跃。按领域分布：

| 领域 | 项目数 |
| --- | ---: |
| 产品 / 业务后端 | 7 |
| 基础设施 / 部署 | 5 |
| AI 工具 / Skill | 4 |
| 数据 / 分析 | 3 |
| ML / 研究 / 论文 | 2 |
| 其他 | 6 |

### Top 项目（脱敏）

| 项目 | Claude | Codex | Git commits | 编排模式 | 领域 |
| --- | ---: | ---: | ---: | --- | --- |
| 项目 A | 83 | 48 | 188 | 双引擎 | 基础设施 / 部署 |
| 项目 B | 42 | 46 | 276 | 双引擎 | 其他（开源工具） |
| 项目 C | 25 | 40 | 101 | 双引擎 | AI 工具 / Skill |
| 项目 D | 5 | 25 | 37 | Codex 主导 | 数据 / 分析 |
| 项目 E | 6 | 22 | 23 | Codex 主导 | 基础设施 / 部署 |
| 项目 F | 0 | 28 | 0 | Codex 主导 | AI 工具 / Skill |
| 项目 G | 15 | 7 | 0 | 双引擎 | ML / 研究 / 论文 |
| 项目 H | 0 | 20 | 21 | Codex 主导 | AI 工具 / Skill |
| 项目 I | 0 | 15 | 10 | Codex 主导 | 产品 / 业务后端 |
| 项目 J | 13 | 0 | 0 | Claude 主导 | 基础设施 / 部署 |
| 项目 K | 7 | 5 | 0 | 双引擎 | 产品 / 业务后端 |
| 项目 L | 7 | 0 | 14 | Claude 主导 | 产品 / 业务后端 |

编排模式统计：**双引擎 5 个** · Codex 主导 5 个 · Claude 主导 2 个

> 核心项目（A/B/C）全部是双引擎运作 —— Claude 做规划和深度推理，Codex 做快速执行和第二意见。这不是平行使用两个工具，而是有意识的分工协作。

## 🧬 Evolution 曲线 — AI 用法在进化

```
2026-01  Codex 起步 · CLI 0.77.0 · 29 threads · 纯 prompt 交互
2026-02  日常化 · CLI 升至 0.107.0-alpha · 22 threads · token 消耗 5× 增长
2026-03  Claude Code 加入(03-09) → 同日解锁 /plan + /resume + /skill-creator
         /compact 首用(03-12) → 上下文管理意识觉醒
         /ssh-prod 首用(03-13) → 自建 skill 投入生产
         /effort 首用(03-17) → 推理深度调节
         109 Codex threads + 261 Claude sessions · 双工具编排成形
2026-04  /vibe-forge 首用(04-07) → Skill 分发生态
         Codex 爆发：232 threads · 3.37B tokens
         CLI 升至 0.122.0-alpha · 日均 10 commit · 多 agent 编排成熟
```

### 月度活跃趋势

| 月份 | Claude sessions | Codex threads | Codex tokens | 里程碑 |
| --- | ---: | ---: | ---: | --- |
| 2026-01 | — | 29 | 38.6M | Codex 起步 |
| 2026-02 | — | 22 | 191.5M | 日常化 |
| 2026-03 | 261 | 109 | 731.6M | Claude 加入 · 双工具编排 |
| 2026-04 | 16+ | 232 | 3.37B | 爆发期 · skill 生态完善 |

> 从 1 月的 29 个 Codex thread 到 4 月的 232 个 + Claude 持续活跃，使用量 **8 倍增长**。更重要的是**质的变化**：从纯 prompt → plan-mode → 自建 skill → 多 agent 编排，每个月都解锁新能力。

## 💡 兴趣主题 & 关键词

从 **55** 份 plan 标题、**390** 条 Codex thread 标题、首条用户消息中自动提取的高频主题：

> **codex** · **claude** · **agent** · **skill** · **deploy** · **github** · **python** · **typescript** · **rust** · **fastapi** · **seo** · **api** · **docker** · **token** · **prompt** · **mcp** · **react** · **billing** · **i18n** · **部署** · **服务** · **接口** · **测试** · **数据** · **论文**

## ⏱️ 工作节奏

### 24 小时活跃热力图（合并 Claude + Codex，本地时间）

```
00 ██████████                32
01 ████████████              38
02 ██▌                        8
03                             0
04                             0
05                             0
06 █▎                         4
07                             0
08 ▎                          1
09 ██▏                        7
10 █████                     16
11 ████████████████████      64
12 ████████████▊             41
13 ████████████              38
14 ████████████▏             39
15 ███████████████▎          49
16 ████████████▏             39
17 ███████████████           48
18 ██████▉                   22
19 █████████████▊            44
20 ████████████              38
21 █████████████▍            43
22 ███████████               35
23 ███████████████████       61
```

> 三段式活跃: **11–17 时**（白天主战场）+ **19–21 时**（晚间冲刺）+ **23–01 时**（深夜独行）。凌晨 2–9 时基本零输入。

### 时间跨度

- 首次活跃: **2026-01-01**（Codex）/ **2026-03-09**（Claude）
- 最近活跃: **2026-04-17**（今天）
- 活跃天数: **~68** 天
- 最长连续活跃: **~21** 天
- 单日峰值: **12,301** 条 Claude 消息（2,243 次 tool 调用）

## 💰 产出 & 投入

### GitHub 同期产出（过去 365 天）

- 总贡献: **798**（commits **651** / PRs **51** / issues **29** / repo contributions **66**）
- 拥有仓库: **71**
- **最高产单日**: 04-03 (66) · 04-02 (52) · 03-29 (44) · 04-04 (33) · 03-28 (29)

#### Top GitHub 仓库

| 仓库 | language | commits | stars |
| --- | --- | ---: | ---: |
| <user>/Repo-Alpha | Python | 276 | 66 |
| <user>/Repo-Beta | Python | 114 | **1,128** |
| <user>/Repo-Gamma | Rust | 38 | 15 |
| <user>/Repo-Delta | TypeScript | 37 | 9 |
| <user>/Repo-Epsilon | — | 24 | **40** |
| <user>/Repo-Zeta | HTML | 22 | 3 |
| <user>/Repo-Eta | TypeScript | 11 | 13 |
| <user>/Repo-Theta | Python | 10 | 4 |
| <user>/Repo-Iota | Jupyter Notebook | 10 | 2 |

### 主要语言（GitHub bytes 排序）

Python (6.08M) · TypeScript (2.28M) · Jupyter Notebook (1.26M) · Rust (0.82M) · JavaScript (0.45M) · HTML (0.37M) · CSS (0.14M) · PLpgSQL (0.11M) · Go (0.07M) · Shell (0.03M)

### 每模型 token 参考

| 模型 | spent tokens | cache-read |
| --- | ---: | ---: |
| GPT-5.4 (Codex) | 3,255,874,561 | — |
| Codex(未标注) | 767,350,122 | — |
| GPT-5.4-mini | 264,066,614 | — |
| Claude Opus 4.6 | 135,018,198 | 3,399,640,688 |
| GPT-5.2-codex | 14,997,492 | — |
| GPT-5.3-codex-spark | 3,408,917 | — |
| Claude Sonnet 4.6 | 2,644,538 | 124,109,847 |

### 单位投入产出（参考）

- 每 commit ≈ **194,164** Claude tokens
- 每行代码 ≈ **340** Claude tokens

> 提醒：AI 产出还包含大量「读代码、改方案、写文档、修测试、做调研」这类不直接转化为 commit 的高价值劳动。

## 📊 数据来源 & 隐私承诺

- **数据 100% 本地**: `~/.claude/*` + `~/.codex/*` + 本地 `git log` + GitHub via `gh`
- **未读取任何对话正文**：只用统计字段、计划 / thread 标题、首条用户消息
- 项目名已匿名（项目 A/B/C…），GitHub 仓库名已替换，API key / token / 邮箱 已正则清洗
- 本报告由 [Readme.skill](https://github.com/study8677/Readme.skill) v2.0 自动生成
- **生成时间**: 2026-04-17
