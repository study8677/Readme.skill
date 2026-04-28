# Readme.skill

> 一份 Claude Code / Codex CLI 的 **skill**：自动生成对外可分享、脱敏的 **AI-Native 开发者 README**。

## 这是什么

Readme.skill 不是一个程序，而是一份给 AI agent 的指令集（即 [`SKILL.md`](./skills/readme-skill/SKILL.md)）。当你在 Claude Code 或 Codex 里说出触发词（如「生成我的 AI 档案」），AI 会按照 SKILL.md 的步骤：

1. 读取本机 `~/.claude/` 与 `~/.codex/` 的统计文件、SQLite、历史 JSONL
2. 调用 `gh` 拉取你的 GitHub 公开贡献日历与 top 仓库
3. 在你的工作目录里跑只读 `git log`，统计本地提交
4. 计算 7 个维度（一览 / AI-Native / 协作风格 / 项目 / 主题 / 节奏 / 投入×产出）
5. 默认匿名脱敏，输出 `output/profile_YYYYMMDD.md`

整个过程**只读、不联网（除 `gh`）**，可放心运行并对外分享结果。对话正文可被读取以提取关键词和协作风格信号，但原文不会写进报告。

## 快速开始

### 方式零：Claude Code Plugin（推荐 —— 两行命令）

在 Claude Code 里：

```
/plugin marketplace add study8677/Readme.skill
/plugin install readme-skill@study8677
```

Claude Code 自动发现 `skills/readme-skill/SKILL.md` 并挂载，无需手动 symlink。需要 Claude Code 支持 plugin 子系统的版本。

### 方式一：Clone + 软链（Codex CLI 或不想用 plugin 的用户）

```bash
git clone https://github.com/study8677/Readme.skill.git
cd Readme.skill

# Claude Code 用户
mkdir -p ~/.claude/skills/readme-skill
ln -sf "$(pwd)/skills/readme-skill/SKILL.md" ~/.claude/skills/readme-skill/SKILL.md

# Codex CLI 用户
mkdir -p ~/.codex/skills/readme-skill
ln -sf "$(pwd)/skills/readme-skill/SKILL.md" ~/.codex/skills/readme-skill/SKILL.md
```

### 方式二：直接复制

```bash
git clone https://github.com/study8677/Readme.skill.git

# Claude Code
mkdir -p ~/.claude/skills/readme-skill
cp Readme.skill/skills/readme-skill/SKILL.md ~/.claude/skills/readme-skill/

# Codex CLI
mkdir -p ~/.codex/skills/readme-skill
cp Readme.skill/skills/readme-skill/SKILL.md ~/.codex/skills/readme-skill/
```

### 方式三：一行 curl

```bash
# Claude Code
mkdir -p ~/.claude/skills/readme-skill && curl -fsSL https://raw.githubusercontent.com/study8677/Readme.skill/main/skills/readme-skill/SKILL.md -o ~/.claude/skills/readme-skill/SKILL.md

# Codex CLI
mkdir -p ~/.codex/skills/readme-skill && curl -fsSL https://raw.githubusercontent.com/study8677/Readme.skill/main/skills/readme-skill/SKILL.md -o ~/.codex/skills/readme-skill/SKILL.md
```

## 使用

安装后在 Claude Code 或 Codex 的对话里说出以下任一句即可触发：

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

两份产物：
1. **Markdown profile** — 详细叙事报告，10 个维度，参考 [`examples/example_profile.md`](./examples/example_profile.md)
2. **SVG 海报**（v2.4 链式传播版） — 一张 1080×1920 竖屏视觉海报，自带传播 3 件套：
   - **A. AI 自评金句**（双行大字，根据数据画像写出有人格的评价）
   - **B. 身份徽章**（顶部 4 胶囊：TWO-ENGINE / CACHE MASTER / SKILL BUILDER / POLYGLOT 等，自动判定）
   - **C. 30 秒安装 CTA**（底部 install 命令 + 仓库 URL，让看到的人能立刻生成自己的）
   - 6 个英雄数字 + Evolution 时间线 + Cache leverage 排行 + Top slash 命令
   - **自动按用户提问语言选择中/英版**：[中文版](./examples/example_poster_zh.svg) · [英文版](./examples/example_poster_en.svg)
   - 技术术语（token / star / commit / model 名 / slash 命令）两版都保留英文，只翻译叙事部分

### Markdown profile 结构

- 个人理念（来自 GitHub bio）
- 一览（关键数字 + velocity 指标）
- 🚀 Velocity & Leverage — AI 让你快了多少、广了多少（v2.0 新增）
- 🤖 AI-Native 实践（多模型编排、高级能力、prompt caching、reasoning effort）
- 🔧 AI 基础设施 — 你造了什么工具给 AI 用（v2.0 新增）
- 🛠️ AI 协作风格（slash 命令 + Session 架构分析）
- 📂 项目与领域分布（脱敏表 + 双工具编排模式）
- 🧬 Evolution 曲线 — AI 用法的成长弧线（v2.0 新增）
- 💡 兴趣主题 & 关键词
- ⏱️ 工作节奏（24h 热力图、连续活跃、峰值日）
- 💰 产出 & 投入（GitHub 贡献优先，token 表降级到参考）
- 📊 数据来源 & 隐私承诺

### 海报转 PNG（用于发社媒）

SVG 是源图，要发微信 / 小红书 / Twitter / Instagram 通常需要 PNG：

```bash
# 方式 1: rsvg-convert （需要 brew install librsvg）
rsvg-convert -h 1920 output/poster_*.svg > poster.png

# 方式 2: 用 Chrome / 浏览器
open output/poster_*.svg     # macOS 默认打开 → 右键另存为 PNG / 截图

# 方式 3: chromium headless
chromium --headless --screenshot=poster.png --window-size=1080,1920 output/poster_*.svg
```

设计原则（来自 v2.4 brief）：3 秒看懂身份 + 6 个证据数字 · 避免 emoji（跨平台字体替换问题）· system-ui 字体 fallback · 数字必须有证据缺则降级。

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

> 缺少某个数据源？没关系。Skill 内置降级策略：没有 Codex SQLite 就跳过 Codex 章节，没有 `gh` 就跳过 GitHub 章节，报告照样生成。

## 隐私

- 全部数据采集发生在**本机**，除 `gh` 调用 GitHub 自身外不联网
- 对话正文（`message.content`）可被读取以增强关键词、协作风格、Session 架构分析，但原文不会写进报告
- 默认对项目名、私有仓库做匿名映射；按 OWASP-style 正则清洗 API key / token / webhook / 邮箱
- skill 不会修改 `~/.claude` 或 `~/.codex` 下任何文件（SQLite 用 `mode=ro&immutable=1` 打开）

## 设计理念

> Skill = 给 AI agent 的指令集，不是替它做事的代码。

[SKILL.md](./skills/readme-skill/SKILL.md) 是唯一的真实交付物。任何"为什么不用一个 Python 脚本一把跑完？"的冲动都被刻意抑制——目的是让 AI 自己理解数据、自己做权衡、自己写出有温度的文字，而不是机械地填模板。

## 协议

MIT
