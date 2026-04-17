# JingWen Fan · AI-Native Developer Profile
> 基于 106 天的本地 Claude Code + Codex 对话数据自动生成 · 2026-04-16
> _个人理念：把复杂问题拆到不可再拆的基本事实或约束，只从这些"不可否认的底层事实"出发进行推理，而不依赖经验、类比或既有结论。_

---

## 一览

- 在 **106** 天里完成 **277** 次 Claude sessions + **357** 次 Codex threads，共 **115,048** 条消息
- Claude 上花费 **137,663,238** tokens，复用 **3,523,750,535** cache-read tokens（cache 比 = **25.6×**）
- Codex 上消耗 **3,222,378,083** tokens
- 同期 GitHub 产出：**645** commits / **51** PRs / **29** issues / **66** repo contributions
- 本地 Git：**943** commits across **38** 仓库（+370,436 / -67,822）
- 主力工具：Claude Code (Opus 4.6 + Sonnet 4.6) + Codex CLI (GPT-5.4)

## 🤖 AI-Native 实践

> 我把 AI 真正纳入了日常开发流程。这不是"偶尔用 ChatGPT 问问"，是把多 LLM 编排、planning、structured workflows 都跑通。

### 多模型编排
| 模型 | sessions / threads | spent tokens | cache-read | 用途倾向 |
| --- | --- | --- | --- | --- |
| Claude Opus 4.6 | 主力（绝大多数 sessions） | 135,018,198 | 3,399,640,688 | 深度推理、复杂规划、plan-mode |
| Claude Sonnet 4.6 | 偶尔批量 | 2,644,538 | 124,109,847 | 快速迭代、批量任务 |
| GPT-5.4 (Codex) | 224 threads | 2,424,947,492 | — | 第二意见、跨工具诊断 |
| GPT-5.4-mini | 9 threads | 11,674,060 | — | 轻量任务 |
| GPT-5.2-codex / 5.3-codex-spark | 5 threads | 18,406,409 | — | 早期 / 实验性模型 |

### 高级能力深度使用
- **Plan-mode**: 启用 **88** 次 — 复杂任务先想清楚再动手，而不是盲目堆 prompt
- **Effort 调节**: **506** 次 — 按任务难度切换推理深度（max / high / medium）
- **Skill 调用**: **15** 次；自研/安装 skills **17** 个 (Claude 11 + Codex 6)
- **Plans 文档**: **55** 份结构化计划；Tasks 跟踪 **51** 个
- **Hooks**: **1** 个；MCP servers: **0** 个；Codex automations: **1** 个

### Prompt caching 熟练度
每花费 1 个新 token，平均复用 **25.6** 个缓存 token —— 反映对长上下文 + 结构化 prompt 的优化能力。

### Reasoning effort 偏好
xhigh **171**（**48%**）· high **46** · medium **17** · low **3** · unspecified **120**

> xhigh 占绝对多数，说明我倾向于让模型多想一会儿、给出更可靠的答案。

## 🛠️ AI 协作风格

### 最常用的 slash 命令 Top 10
| # | 命令 | 次数 | 含义 |
| --- | --- | --- | --- |
| 1 | /effort | 506 | 切换推理深度 |
| 2 | /usage | 122 | 检查 token 用量 |
| 3 | /plan | 88 | 进入 plan-mode |
| 4 | /btw | 59 | 后台轨迹切换 |
| 5 | /clear | 51 | 清空对话上下文 |
| 6 | /resume | 50 | 恢复历史 session |
| 7 | /compact | 25 | 压缩上下文 |
| 8 | /vibe-forge | 20 | 自研 skill |
| 9 | /init | 13 | 初始化项目 CLAUDE.md |
| 10 | /ssh-prod | 10 | 自研 prod 巡检 skill |

### 节奏特征
- **Plan→Direct 比例**: 每 100 条直接 prompt 中有 **2.9** 次 plan-mode（先想再做的习惯已成为肌肉记忆）
- **平均会话深度**: 每个 session **415.3** 条消息 —— 倾向于长链路深度协作
- **最长 session**: **172.9** 小时 / **1,444** 条消息（连续多日的复杂任务）

## 📂 项目与领域分布

跨 **45+** 个项目活跃，按领域分布：

| 领域 | 项目数 |
| --- | --- |
| 数据 / 分析 | 32 |
| 产品 / 业务后端 | 19 |
| 其他 | 14 |
| AI 工具 / Skill | 12 |
| 基础设施 / 部署 | 8 |
| ML / 研究 / 论文 | 5 |
| 文档 / Markdown | 3 |

### Top 项目（脱敏）
| 项目 | Claude sessions | Codex threads | Git commits | 领域 |
| --- | --- | --- | --- | --- |
| 项目 A | 95 | 47 | 188 | 基础设施 / 部署 |
| 项目 B | 42 | 46 | 274 | 其他（开源工具） |
| 项目 C | 26 | 40 | 90 | AI 工具 / Skill |
| 项目 D | 0 | 2 | 188 | 基础设施 / 部署 |
| 项目 E | 5 | 25 | 37 | 数据 / 分析 |
| 项目 F | 6 | 22 | 23 | 基础设施 / 部署 |
| 项目 G | 3 | 20 | 21 | AI 工具 / Skill |
| 项目 H | 17 | 7 | 0 | ML / 研究 / 论文 |
| 项目 I | 4 | 15 | 10 | 产品 / 业务后端 |
| 项目 J | 13 | 2 | 0 | 产品 / 业务后端 |

## 💡 兴趣主题 & 关键词

从 plan 标题、Codex thread 标题、prompt 文本中自动提取的高频主题：

> **codex** · **agent** · **github** · **fastapi** · **python** · **数据** · **rust** · **prompt** · **claude** · **deploy** · **api** · **react** · **rl** · **token** · **typescript** · **docker** · **mcp** · **测试** · **服务** · **接口**

## ⏱️ 工作节奏

### 24 小时活跃热力图
```
00 ▏    12
01 █    32
02 ███▌  85
03 █████████████████  367
04 █████████  199
05 ████████████████  339
06 █████████████████  350
07 ██████████████████  379
08 ████████████████  336
09 ████████████████▌ 358
10 ████████  176
11 ███████████████▌  329
12 ███████████████████  407
13 ██████████████████  389
14 ████████████  256
15 ███████████▌  248
16 █████████  189
17 ██████  124
18 ███   60
19 █▌    30
20 █     19
21 █     20
22 ▏    13
23 ▏    13
```
（峰值时段 **3-7 时 + 12-15 时**；典型的"早间深思 + 午后快速迭代"作息）

### 时间跨度
- 首次活跃: **2026-01-01**（Codex）/ **2026-03-09**（Claude）
- 最近活跃: **2026-04-16**
- 活跃天数: **66**
- 最长连续活跃: **40** 天
- 单日峰值: **2026-03-18**, **12,301** 条消息

## 💰 AI 投入 × 产出

### 每模型 token 总账
| 模型 | spent (新付费) | cache-read (复用) |
| --- | --- | --- |
| Claude Opus 4.6 | 135,018,198 | 3,399,640,688 |
| Claude Sonnet 4.6 | 2,644,538 | 124,109,847 |
| GPT-5.4 | 2,424,947,492 | — |
| GPT-5.4-mini | 11,674,060 | — |
| GPT-5.3-codex-spark | 3,408,917 | — |
| GPT-5.2-codex | 14,997,492 | — |

### GitHub 同期产出（过去 365 天）
- 总贡献: **792**（其中 commits **645** / PRs **51** / issues **29** / repo contribs **66**）
- 拥有仓库总数: **71**

#### Top 仓库（窗口内 commits）
| 仓库 | language | commits | private |
| --- | --- | --- | --- |
| study8677/OpenCMO | Python | 274 | no |
| study8677/antigravity-workspace-template | Python | 112 | no |
| study8677/vibe-forge | Rust | 38 | no |
| study8677/ReadYourUsers | TypeScript | 37 | no |
| study8677/study8677 | (profile readme) | 30 | no |
| study8677/easy-claude-code | — | 24 | no |
| study8677/724claw.icu | HTML | 22 | no |
| study8677/clawdbot-webchat-lite | TypeScript | 11 | no |

### 主要语言（GitHub bytes 排序）
Python · TypeScript · Rust · HTML · JavaScript · Markdown · Shell · CSS

### 单位投入产出（粗略估算）
- 每个本地 commit ≈ **145,984** Claude tokens
- 每行代码（add+del）≈ **314** Claude tokens

> 提醒：这是粗略估算，AI 的产出还包含大量「读代码、改方案、写文档、修测试」这类不直接转化为 commit 的高价值劳动。

## 📊 数据来源 & 隐私承诺

- 数据 100% 本地：`~/.claude/*` + `~/.codex/*` + 本地 `git log` + GitHub via `gh` (只查询)
- **未读取任何对话正文**，只用统计字段、计划 / thread 标题、首条用户消息
- 项目名、文件路径已匿名（项目 A/B/C…），API key / token / 邮箱 已正则清洗
- 报告由 Claude Code 按 [Readme.skill](https://github.com/study8677/Readme.skill) 自动生成，可重复运行
- 生成时间: **2026-04-16T19:04:41+08:00**
