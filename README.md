# Readme.skill

> 一份 Claude Code / Codex CLI 的 **skill**：自动生成对外可分享、脱敏的 **AI-Native 开发者 README**。

## 这是什么

Readme.skill 不是一个程序，而是一份给 AI agent 的指令集（即 [`SKILL.md`](./SKILL.md)）。当你在 Claude Code 或 Codex 里说出触发词（如「生成我的 AI 档案」），AI 会按照 SKILL.md 的步骤：

1. 读取本机 `~/.claude/` 与 `~/.codex/` 的统计文件、SQLite、历史 JSONL
2. 调用 `gh` 拉取你的 GitHub 公开贡献日历与 top 仓库
3. 在你登记过的工作目录里跑只读 `git log`，统计本地提交
4. 计算 7 个维度（一览 / AI-Native / 协作风格 / 项目 / 主题 / 节奏 / 投入×产出）
5. 默认匿名脱敏，输出 `output/profile_YYYYMMDD.md`

整个过程**只读、不联网（除 `gh`）、不读对话正文**，可放心运行并对外分享结果。

## 安装

把这个 skill 放到 Claude Code / Codex 能识别的 skills 目录之一即可：

```bash
# Claude Code
ln -s "$(pwd)/SKILL.md" ~/.claude/skills/readme-skill/SKILL.md
mkdir -p ~/.claude/skills/readme-skill
ln -sf "$(pwd)/SKILL.md" ~/.claude/skills/readme-skill/SKILL.md

# Codex CLI
mkdir -p ~/.codex/skills/readme-skill
ln -sf "$(pwd)/SKILL.md" ~/.codex/skills/readme-skill/SKILL.md
```

或者直接复制：

```bash
cp -r . ~/.claude/skills/readme-skill/
```

## 使用

在 Claude Code 或 Codex 的对话里说出以下任一句即可触发：

- "生成我的 AI 档案"
- "做一份 AI-native README"
- "分析我的 Claude / Codex 使用情况"
- "build my AI usage profile"
- "summarize my Claude / Codex history"

AI 会跑完整套流程，把结果写到 `output/profile_<日期>.md`。

### 私人版 vs 分享版

- **默认（分享版）** — 项目名匿名为「项目 A/B/C」，私有仓库改为「Private Repo X」
- **私人版** — 加一句 "私人版 / 不要脱敏 / show real names"，AI 会保留真实名字（仍会脱敏 API key、邮箱）

## 输出长什么样

参考 [`examples/example_profile.md`](./examples/example_profile.md)。结构：

- 个人理念（来自 GitHub bio）
- 一览（关键数字）
- 🤖 AI-Native 实践（多模型编排、高级能力、prompt caching、reasoning effort）
- 🛠️ AI 协作风格（top slash 命令、plan-mode 比例、最长 session）
- 📂 项目与领域分布（脱敏表 + 领域桶）
- 💡 兴趣主题 & 关键词
- ⏱️ 工作节奏（24h 热力图、连续活跃、峰值日）
- 💰 AI 投入 × 产出（每模型 token、GitHub 贡献、Top 仓库、语言分布）
- 📊 数据来源 & 隐私承诺

## 数据来源

| 来源 | 路径 | 用途 |
| --- | --- | --- |
| Claude Code 预聚合 | `~/.claude/stats-cache.json` | sessions / messages / tokens / hour heatmap |
| Claude 命令历史 | `~/.claude/history.jsonl` | slash 命令热度、项目归属 |
| Claude 项目 | `~/.claude/projects/<encoded>/*.jsonl` | 每项目 session 计数 + 真实 cwd 还原 |
| Claude 计划 | `~/.claude/plans/*.md` | plan 标题（关键词语料） |
| Claude skills | `~/.claude/skills/` | 自研 skill 数 |
| Codex 主库 | `~/.codex/state_5.sqlite` (read-only) | threads 表的全部统计 |
| Codex 命令 | `~/.codex/history.jsonl` | prompt 文本采样 |
| GitHub | `gh api graphql` | 365 天 contributions、top repos、语言 |
| 本地 Git | 候选目录的 `git log` | commits / additions / deletions |

## 隐私

- 全部数据采集发生在**本机**，除 `gh` 调用 GitHub 自身外不联网
- 永不读取 `message.content` 等对话正文字段
- 默认对项目名、私有仓库做匿名映射；按 OWASP-style 正则清洗 API key / token / webhook / 邮箱
- skill 不会修改 `~/.claude` 或 `~/.codex` 下任何文件（SQLite 用 `mode=ro&immutable=1` 打开）

## 设计理念

> Skill = 给 AI agent 的指令集，不是替它做事的代码。

[SKILL.md](./SKILL.md) 是唯一的真实交付物。任何"为什么不用一个 Python 脚本一把跑完？"的冲动都被刻意抑制——目的是让 AI 自己理解数据、自己做权衡、自己写出有温度的文字，而不是机械地填模板。

## 协议

MIT
