# Alex · AI-Native Developer Profile
> 基于 90 天的本地 Claude Code + Codex 对话数据自动生成 · 2026-04-17
> _个人理念：Write code for humans first, machines second._

---

## 一览

- 在 **90** 天里完成 **150** 次 Claude sessions + **200** 次 Codex threads，共 **50,000** 条消息
- Claude 上花费 **80,000,000** spent tokens，复用 **1,200,000,000** cache-read tokens（cache 比 = **15.0×**）
- Codex 上消耗 **1,500,000,000** tokens
- 同期 GitHub 产出：**320** commits / **25** PRs / **15** issues / **30** repo contributions
- 本地 Git：**480** commits across **20** 仓库（+180,000 / -35,000）
- 主力工具：Claude Code (Opus 4.6 + Sonnet 4.6) + Codex CLI (GPT-5.4)

## 🤖 AI-Native 实践

> 把 AI 真正接进了日常开发流程。多 LLM 编排、planning、structured workflows 都跑通。

### 多模型编排
| 模型 | sessions / threads | spent tokens | cache-read | 用途倾向 |
| --- | ---: | ---: | ---: | --- |
| Claude Opus 4.6 | 主力 | 78,000,000 | 1,150,000,000 | 深度推理、复杂规划 |
| Claude Sonnet 4.6 | 偶尔 | 2,000,000 | 50,000,000 | 快速迭代、批量任务 |
| GPT-5.4 (Codex) | 130 threads | 1,200,000,000 | — | 第二意见、跨工具诊断 |
| GPT-5.4-mini | 12 threads | 8,000,000 | — | 轻量任务 |

### 高级能力深度使用
- **Plan-mode**: 启用 **45** 次
- **Effort 调节**: **280** 次
- **Skill 调用 & 自研**: **10** 个 skills（Claude 6 + Codex 4）
- **Plan 文档**: **30** 份；Tasks 跟踪 **25** 个
- **Hooks**: **2** 个；MCP servers: **1** 个

### Prompt caching 熟练度
每花费 1 个新 token，平均复用 **15.0** 个缓存 token。

### Reasoning effort 偏好
xhigh **80**（**40%**）· high **50** · medium **30** · low **10** · unspecified **30**

## 🛠️ AI 协作风格

### 最常用的 slash 命令 Top 10
| # | 命令 | 次数 | 含义 |
| --- | --- | ---: | --- |
| 1 | /effort | 280 | 切换推理深度 |
| 2 | /usage | 65 | 检查 token 用量 |
| 3 | /plan | 45 | 进入 plan-mode |
| 4 | /clear | 30 | 清空对话上下文 |
| 5 | /resume | 28 | 恢复历史 session |
| 6 | /compact | 15 | 压缩上下文 |
| 7 | /init | 8 | 初始化 CLAUDE.md |
| 8 | /model | 6 | 切换模型 |

### 节奏特征
- **Plan→Direct 比例**: 每 100 条 prompt 中 **3.2** 次 plan-mode
- **平均会话深度**: 每个 session **333** 条消息
- **最长 session**: **48.5 小时** / **620** 条消息

## 📂 项目与领域分布

跨 **20** 个项目活跃：

| 领域 | 项目数 |
| --- | ---: |
| 产品 / 业务后端 | 7 |
| 基础设施 / 部署 | 4 |
| AI 工具 / Skill | 3 |
| 数据 / 分析 | 3 |
| ML / 研究 / 论文 | 2 |
| 文档 / Markdown | 1 |

### Top 项目（脱敏）
| 项目 | Claude sessions | Codex threads | Git commits | 领域 |
| --- | ---: | ---: | ---: | --- |
| 项目 A | 50 | 30 | 120 | 产品 / 业务后端 |
| 项目 B | 30 | 25 | 95 | 基础设施 / 部署 |
| 项目 C | 20 | 35 | 60 | AI 工具 / Skill |
| 项目 D | 15 | 20 | 45 | 数据 / 分析 |
| 项目 E | 10 | 15 | 30 | ML / 研究 / 论文 |

## 💡 兴趣主题 & 关键词

> **python** · **typescript** · **fastapi** · **deploy** · **agent** · **api** · **docker** · **react** · **token** · **测试** · **服务** · **接口**

## ⏱️ 工作节奏

### 24 小时活跃热力图
```
00 ██████          60
01 ███             30
02 █               10
03                  0
04                  0
05                  2
06 █               10
07 ██              20
08 ████            40
09 ████████        80
10 ██████████     100
11 ████████████   120
12 █████████████  130
13 ████████████   120
14 ██████████     100
15 █████████       90
16 ████████        80
17 ██████          60
18 ████            40
19 ██████          60
20 ████████        80
21 ██████          60
22 ████            40
23 ███             30
```
（峰值时段 **10–14 时 + 19–21 时**）

### 时间跨度
- 首次活跃: **2026-01-15**
- 最近活跃: **2026-04-17**
- 活跃天数: **72**
- 最长连续活跃: **25** 天
- 单日峰值: **2026-03-20**, **5,200** 条消息

## 💰 AI 投入 × 产出

### 每模型 token 总账
| 模型 | spent (新付费) | cache-read (复用) |
| --- | ---: | ---: |
| Claude Opus 4.6 | 78,000,000 | 1,150,000,000 |
| Claude Sonnet 4.6 | 2,000,000 | 50,000,000 |
| GPT-5.4 | 1,200,000,000 | — |
| GPT-5.4-mini | 8,000,000 | — |

### GitHub 同期产出（过去 365 天）
- 总贡献: **420**（commits **320** / PRs **25** / issues **15**）
- 拥有仓库: **35**

#### Top 仓库（窗口内 commits）
| 仓库 | language | commits | private |
| --- | --- | ---: | --- |
| user/project-alpha | TypeScript | 95 | no |
| user/project-beta | Python | 80 | no |
| Private Repo X | Go | 45 | yes |
| user/project-gamma | Rust | 30 | no |

### 主要语言
TypeScript · Python · Rust · Go · Shell

### 单位投入产出（粗略估算）
- 每个本地 commit ≈ **166,667** Claude tokens
- 每行代码（add+del）≈ **372** Claude tokens

## 📊 数据来源 & 隐私承诺

- 数据 100% 本地：`~/.claude/*` + `~/.codex/*` + 本地 `git log` + GitHub via `gh` (只查询)
- **未读取任何对话正文**，只用统计字段、计划 / thread 标题、首条用户消息
- 项目名、文件路径已匿名（项目 A/B/C…），API key / token / 邮箱 已正则清洗
- 报告由 Claude Code 按 Readme.skill 自动生成，可重复运行
- 生成时间: **2026-04-17T10:00:00+08:00**
