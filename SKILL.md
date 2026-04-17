---
name: readme-skill
description: >
  生成一份对外可分享、脱敏的 AI-Native 开发者 README。
  量化展示我对 Claude Code + Codex CLI 的使用深度、AI 协作风格、项目与领域分布、
  兴趣主题，以及与 GitHub 提交的产出关联。
  Trigger when the user says: "生成我的 AI 档案" / "做一份 AI-native README" /
  "分析我的 Claude 使用情况" / "总结我的 AI 使用" / "build my AI usage profile"
  / "summarize my Claude / Codex history" / "生成开发者画像".
  全程本地、只读、默认匿名；不会读取任何对话正文，不会上传任何数据。
license: MIT
---

# Readme.skill — AI-Native 开发者档案生成器

You (the AI agent invoking this skill) will read local Claude Code + Codex data,
compute a fixed set of dimensions, and render a Chinese Markdown profile under
`./output/`. **You do all of the work** — read the files with `Read`, query
sqlite via `Bash`, and synthesize the prose yourself. Do **not** write helper
scripts; the skill is the recipe.

> 默认行为：**对外分享版** —— 项目名匿名、敏感信息脱敏。
> 如果用户明确说"私人版 / 不要脱敏 / show real names"，跳过匿名步骤。

---

## Step 1 — 准备

```bash
cd <repo-with-this-skill>   # e.g. ~/Projects/Readme.skill
mkdir -p output
DATE=$(date +%Y%m%d)
```

Decide anonymization mode (default = on). Build an in-memory mapping
`real_path → "项目 A/B/C"` as you encounter project paths in later steps.
Use the same mapping consistently across all sections.

---

## Step 2 — 读取 Claude Code 数据 (`~/.claude/`)

### 2.1 预聚合统计（最权威，先看这个）

Read `~/.claude/stats-cache.json`. Extract:

| 字段 | 含义 |
| --- | --- |
| `totalSessions` | session 总数 |
| `totalMessages` | 消息总数 |
| `firstSessionDate` | 首个 session ISO 时间 |
| `longestSession.{duration,messageCount,timestamp}` | 最长 session |
| `hourCounts` | `{hour: count}` 24h 热力 |
| `modelUsage[model].{inputTokens,outputTokens,cacheReadInputTokens,cacheCreationInputTokens}` | 每模型 token 细分 |
| `dailyActivity[].{date,messageCount,sessionCount,toolCallCount}` | 每日活跃 |
| `dailyModelTokens[].{date,tokensByModel}` | 每日按模型 token |

**派生量（你来算）**:
- `claude_tokens_spent = Σ (inputTokens + outputTokens + cacheCreationInputTokens)` —— 真实新付费 token
- `claude_cache_read = Σ cacheReadInputTokens` —— 缓存复用，反映 prompt-caching 熟练度
- `cache_to_spent_ratio = claude_cache_read / claude_tokens_spent` —— 比值越大越熟

### 2.2 Slash-command 热度

`~/.claude/history.jsonl` —— 每行 `{display, timestamp, project, sessionId}`。

```bash
# Top 15 slash commands
jq -r 'select(.display | startswith("/")) | (.display | split(" ")[0])' \
   ~/.claude/history.jsonl | sort | uniq -c | sort -rn | head -15

# 总条数 vs 命令条数 vs 直接 prompt 条数
total=$(wc -l < ~/.claude/history.jsonl)
cmd=$(jq -r 'select(.display | startswith("/")) | .display' ~/.claude/history.jsonl | wc -l)
echo "total=$total cmd=$cmd plain=$((total - cmd))"
```

记录：`/effort`、`/plan`、`/skill*`、`/usage`、`/clear`、`/resume`、`/compact`、`/init` 各自次数。

### 2.3 项目分布 (`~/.claude/projects/`)

Each subdir is one project; per-project `*.jsonl` files = sessions.
The dir name encodes the absolute path with `/` → `-` (ambiguous when the
original path itself contains `-`).

```bash
# Top 15 by session-file count
for d in ~/.claude/projects/*/; do
  n=$(ls "$d"*.jsonl 2>/dev/null | wc -l | tr -d ' ')
  echo "$n $(basename "$d")"
done | sort -rn | head -15
```

To recover the canonical real path (so you can run `git log` later), read
the `cwd` field from the first JSONL in each dir:

```bash
head -1 ~/.claude/projects/<encoded>/*.jsonl 2>/dev/null \
  | jq -r 'select(.cwd) | .cwd' | head -1
```

### 2.4 计划与 skill 自研

```bash
# Plan titles (first # heading of each plan)
for f in ~/.claude/plans/*.md; do
  head -2 "$f" | grep -m1 '^# ' | sed 's/^# //'
done

ls ~/.claude/skills/ | wc -l       # skills installed / authored
ls ~/.claude/tasks/  | wc -l        # tasks tracked
ls ~/.claude/todos/  | wc -l
```

### 2.5 配置深度

Read `~/.claude/settings.json`. Count:
- `hooks` 个数（结构化自动化能力）
- `mcpServers` 个数（外部能力接入）
- `permissions.defaultMode`

---

## Step 3 — 读取 Codex CLI 数据 (`~/.codex/`)

### 3.1 SQLite (read-only)

The primary analytics store is `~/.codex/state_5.sqlite`, table `threads`.
**Always open with `mode=ro`** so you can never write:

```bash
SQ='sqlite3 file:'"$HOME"'/.codex/state_5.sqlite?mode=ro&immutable=1'

# Aggregate
$SQ "SELECT COUNT(*), SUM(tokens_used), MIN(created_at), MAX(created_at) FROM threads;"

# Model breakdown (note: empty/NULL model = older sessions, label as 'Codex (未标注)')
$SQ "SELECT COALESCE(NULLIF(model,''),'Codex(未标注)'), COUNT(*), SUM(tokens_used) \
     FROM threads GROUP BY 1 ORDER BY 3 DESC;"

# Reasoning effort distribution (xhigh / high / medium / low / unspecified)
$SQ "SELECT COALESCE(NULLIF(reasoning_effort,''),'unspecified'), COUNT(*) \
     FROM threads GROUP BY 1 ORDER BY 2 DESC;"

# Top 15 working dirs
$SQ "SELECT cwd, COUNT(*), SUM(tokens_used) FROM threads \
     WHERE cwd != '' GROUP BY cwd ORDER BY 2 DESC LIMIT 15;"

# Hour-of-day heatmap
$SQ "SELECT strftime('%H', datetime(created_at,'unixepoch')), COUNT(*) \
     FROM threads GROUP BY 1 ORDER BY 1;"

# Day-of-activity timeseries
$SQ "SELECT date(created_at,'unixepoch'), COUNT(*) FROM threads GROUP BY 1;"

# Sample titles + first user messages for keyword extraction (titles only — no body)
$SQ "SELECT title FROM threads WHERE title != '' ORDER BY created_at DESC LIMIT 200;"
$SQ "SELECT first_user_message FROM threads WHERE first_user_message != '' \
     ORDER BY created_at DESC LIMIT 200;"

# CLI versions used (Codex evolution signal)
$SQ "SELECT cli_version, COUNT(*) FROM threads WHERE cli_version != '' \
     GROUP BY 1 ORDER BY 2 DESC LIMIT 10;"
```

### 3.2 Codex 全局历史

`~/.codex/history.jsonl` — `{session_id, ts, text}`. Sample for keywords:

```bash
wc -l ~/.codex/history.jsonl                                 # total prompts
jq -r '.text' ~/.codex/history.jsonl | head -300 > /tmp/codex_text.txt   # corpus
jq -r '.session_id' ~/.codex/history.jsonl | sort -u | wc -l # distinct sessions
```

### 3.3 自研 artifacts

```bash
ls ~/.codex/skills/      | wc -l   # codex skills
ls ~/.codex/automations/ | wc -l   # scheduled automations
ls ~/.codex/rules/       | wc -l   # custom rules
```

---

## Step 4 — GitHub (via `gh`)

```bash
gh auth status >/dev/null 2>&1 || { echo "gh not auth'd, skipping"; }
```

If authenticated:

```bash
# Last-365-day contributions + top repos in window
gh api graphql -f query='
query($from: DateTime!, $to: DateTime!) {
  viewer {
    login name bio
    contributionsCollection(from: $from, to: $to) {
      totalCommitContributions
      totalPullRequestContributions
      totalIssueContributions
      totalRepositoryContributions
      totalPullRequestReviewContributions
      restrictedContributionsCount
      contributionCalendar { totalContributions
        weeks { contributionDays { date contributionCount } } }
      commitContributionsByRepository(maxRepositories: 25) {
        contributions { totalCount }
        repository { nameWithOwner isPrivate isFork stargazerCount
                     primaryLanguage { name } }
      }
    }
    repositories(first: 1, ownerAffiliations: OWNER) { totalCount }
    pullRequests(first: 1) { totalCount }
    issues(first: 1) { totalCount }
  }
}' -F from="$(date -u -v -365d +%Y-%m-%dT00:00:00Z)" \
   -F to="$(date -u +%Y-%m-%dT00:00:00Z)"
```

Then page through repositories for language bytes (up to 5 pages × 100 repos):

```bash
gh api graphql -f query='
query($cursor: String) {
  viewer { repositories(first: 100, after: $cursor, ownerAffiliations: OWNER,
                         isFork: false, orderBy: {field: UPDATED_AT, direction: DESC}) {
    pageInfo { hasNextPage endCursor }
    nodes { nameWithOwner isPrivate stargazerCount
            languages(first: 10, orderBy: {field: SIZE, direction: DESC}) {
              edges { size node { name } } } }
  } } }' -F cursor=""
```

Aggregate languages by `Σ size` per language across all repos.

---

## Step 5 — 本地 Git 提交

Build the candidate path set from:
1. Real `cwd` recovered for each `~/.claude/projects/<encoded>/`
2. `cwd` column from Codex `threads` table

For each path that's a git repo, count the **current user's** commits in the
past year:

```bash
me=$(git config --global user.email)

for path in <candidate-paths>; do
  [ -d "$path/.git" ] || continue
  git -C "$path" log --since=1.year.ago --author="$me" \
      --numstat --no-renames --pretty=format:'COMMIT|%H|%aI'
done
```

Aggregate:
- `commits` (count of `COMMIT|` lines)
- `additions`, `deletions` (sum the numstat columns)
- `last_commit_iso`
- Per-extension LOC (count `+` `-` per file extension → top 10 languages)

---

## Step 6 — 计算 7 个维度（你做推理，不要写脚本）

### 6.1 一览
- 总活跃天数 = unique union of all dates from
  `dailyActivity`, codex `by_date`, history `by_date`
- 跨度 = `min..max` of those dates
- 总 sessions / 总消息 / 总 tokens (Claude) / 总 threads / 总 tokens (Codex)
- 同期 GitHub: commits, PRs, issues, calendar_total
- 本地 git: commits / +additions / −deletions / repos

### 6.2 AI-Native 实践（核心章节）
- **多模型编排**: 列出每个模型的 spent / cache_read tokens
- **高级能力使用**: plan-mode 次数、effort 调节次数、skill 调用次数、
  自研 skills 数、hooks/MCP 数、plans/tasks 数、automations 数
- **Prompt caching 熟练度**: cache_to_spent_ratio
- **Reasoning effort 分布**: xhigh/high/medium/low 占比

### 6.3 协作风格
- Top 10 slash commands (cmd, count, 简短解读)
- Plan-to-direct ratio = `/plan count / non-command prompt count`
- 平均消息/session = `totalMessages / totalSessions`
- 最长 session: 时长(小时) + 消息数

### 6.4 项目与领域
- 合并维度: each project key (real_cwd) accumulates
  `sessions`(claude) + `codex_threads` + `git_commits` + `git_lines`
- 综合分数 = `sessions*5 + codex_threads*4 + git_commits`
- 排序取 Top 12，匿名化为 "项目 A/B/C..."（按分数顺序）
- 用以下规则给每个项目打**领域标签**（命中第一条即停）：

| 领域 | 关键词（小写匹配 cwd basename + 标题） |
| --- | --- |
| ML / RL / 论文 | rllunwen, rl-, lunwen, paper, thesis, 论文, danzi, 大创 |
| 基础设施 / 部署 | router, deploy, infra, ops, monitor, k8s, ci-cd |
| AI 工具 / Skill | skill, claude, codex, agent, antigravity, easy_claude, vibe-forge |
| 产品 / 业务后端 | teamo, askmany, service, backend, api, ama-, steward |
| 数据 / 分析 | readyourusers, analytics, data, bi-project, bibili |
| 文档 / Markdown | readme, doc, documents |
| 其他 | （fallback） |

### 6.5 兴趣主题 & 关键词

**Corpus**: 拼接 plan titles + Codex thread titles + first_user_messages
+ 部分 history.text。

**Tokenize**:
- 英文：`[A-Za-z][A-Za-z0-9_-]+`，小写化，长度 ≥ 2，去停用词
- 中文：抽取 `[\u4e00-\u9fff]+` 串，做 2-char 滑窗，每个 chunk 内去重；
  过滤明显碎片（如"前项""解当""目了"），过滤含纯停用字的二元

英文停用词（精简）：the, a, an, is, are, of, in, on, for, to, by, from, with,
this, that, it, you, we, they, do, does, please, help, use, used, plan,
make, get, just, also, will, would, can.

中文停用字：的 了 是 在 和 有 不 就 也 为 以 对 把 被 从 等 都 这 那 个 啊 呢
吧 呀 之 与 或 及 并 要 做 能 会 上 下 里 们 好 之 吗 一 也 就 都 还 到 去 给
跟 向 自 什 么 怎 哪 如 何 因 所 然 后 比 例 而 且 但 不 过 或 还 关 通 基 由
得 着 过 看 想 说 点 种 次 时 年 月 日 中 时 间 现 在.

**输出**：top 30 keywords，过滤掉只剩 1 出现的，过滤包含纯英文 stop-only 字符的。
用作"标签云"展示：`tag1·N　tag2·M　tag3·K`。

### 6.6 节奏
- **24h 热力**: 合并 `hourCounts` (claude) + codex `by_hour` + history `by_hour`
- **活跃天数**: union of dates；连续活跃 streak = 最长连续 1 天间隔的串
- **峰值日**: max 的 dailyActivity.messageCount
- **首次/最近**: min/max date

### 6.7 投入 × 产出（核心章节）
- 每模型 spent / cache_read 表
- 每日 token 消耗趋势（用 `dailyModelTokens` 求和；可绘 ASCII 曲线）
- GitHub 1 年贡献日历（求 calendar_total，列出 top 10 高产日）
- Top GitHub 仓库（commits 排序；private 仓库改名为 "Private Repo X"）
- 主要语言：merge `gh languages.bytes` 与本地 `git numstat ext` 排序
- **单位投入产出**:
  - `tokens_per_commit = claude_tokens_spent / git_local_commits`
  - `tokens_per_loc    = claude_tokens_spent / (additions + deletions)`

---

## Step 7 — 隐私脱敏（默认开）

Before writing the README, scan all string fields for these regex and replace
with `<REDACTED:type>`:

| pattern | replacement |
| --- | --- |
| `sk-[A-Za-z0-9_-]{20,}` | `<REDACTED:openai-key>` |
| `sk-ant-[A-Za-z0-9_-]{20,}` | `<REDACTED:anthropic-key>` |
| `gh[oprs]_[A-Za-z0-9]{20,}` | `<REDACTED:github-token>` |
| `github_pat_[A-Za-z0-9_]{20,}` | `<REDACTED:github-pat>` |
| `AKIA[0-9A-Z]{16}` | `<REDACTED:aws-key>` |
| `xox[baprs]-[A-Za-z0-9-]{10,}` | `<REDACTED:slack-token>` |
| `https://open.feishu.cn/open-apis/bot/v2/hook/[A-Za-z0-9-]+` | `<REDACTED:feishu-webhook>` |
| `\b[\w.+-]+@[\w-]+\.[\w.-]+\b` | `<REDACTED:email>` |

Project name handling (anonymize=on):
- For every real project path you discovered, allocate a stable label `项目 A/B/C/...`
  in **descending order of comprehensive score** (Step 6.4)
- Use the label everywhere the project would otherwise appear by name
- For private GitHub repos, use `Private Repo X`
- File paths: only show `basename`, never the absolute path

If user said "show real names" / "私人版":
- Skip the anonymization step (still scrub secrets)
- Keep tokens / keys redaction always on

---

## Step 8 — 渲染最终 Markdown

Write to `output/profile_<YYYYMMDD>.md` using **exactly** this structure
(中文叙事 + 表格；技术词保留英文；体感舒服优先于堆数据)：

```markdown
# <name or github_login> · AI-Native Developer Profile
> 基于 <span_days> 天的本地 Claude Code + Codex 对话数据自动生成 · <generated_at>
> _个人理念：<github bio if available>_

---

## 一览

- 在 **<span_days>** 天里完成 **<claude_sessions>** 次 Claude sessions + **<codex_threads>** 次 Codex threads，共 **<total_messages>** 条消息
- Claude 上花费 **<claude_tokens_spent>** tokens，复用 **<cache_read>** tokens（cache 比 = **<ratio>**×）
- Codex 上消耗 **<codex_tokens>** tokens
- 同期 GitHub 产出：**<github_commits>** commits / **<github_prs>** PRs / **<github_issues>** issues / **<repo_contribs>** repos
- 本地 Git：**<git_commits>** commits across **<git_repos>** 仓库（+<additions> / -<deletions>）
- 主力工具：Claude Code (Opus 4.6 + Sonnet 4.6) + Codex CLI (GPT-5.4)

## 🤖 AI-Native 实践

> 我把 AI 真正纳入了日常开发流程。这不是"偶尔用 ChatGPT 问问"，是把多 LLM 编排、planning、structured workflows 都跑通。

### 多模型编排
| 模型 | sessions / threads | spent tokens | cache-read | 用途倾向 |
| --- | --- | --- | --- | --- |
| Claude Opus 4.6 | … | … | … | 深度推理、复杂规划 |
| Claude Sonnet 4.6 | … | … | … | 快速迭代、批量任务 |
| GPT-5.4 (Codex) | … | … | — | 第二意见、跨工具诊断 |
| GPT-5.4-mini | … | … | — | 轻量任务 |

### 高级能力深度使用
- **Plan-mode**: 启用 **<n>** 次 — 复杂任务先想清楚再动手
- **Effort 调节**: **<n>** 次 — 按任务难度切换推理深度
- **Skill 调用**: **<n>** 次；自研/安装 skills **<n+m>** 个 (Claude <n> + Codex <m>)
- **Plans 文档**: **<n>** 份结构化计划；Tasks 跟踪 **<n>** 个
- **Hooks**: **<n>** 个；MCP servers: **<n>** 个；Codex automations: **<n>** 个

### Prompt caching 熟练度
每花费 1 个新 token，平均复用 **<ratio>** 个缓存 token —— 反映对长上下文 + 重复结构的优化能力。

### Reasoning effort 偏好
xhigh **<n>**（**<%>**）· high **<n>** · medium **<n>** · low **<n>**

## 🛠️ AI 协作风格

### 最常用的 slash 命令 Top 10
| # | 命令 | 次数 | 含义 |
| --- | --- | --- | --- |
| 1 | /effort | 506 | 切换推理深度（max/high/medium） |
| 2 | … | … | … |

### 节奏特征
- **Plan→Direct 比例**: 每 100 条直接对话中有 **<n>** 次 plan-mode（先想再做）
- **平均会话深度**: 每个 session **<n>** 条消息
- **最长 session**: **<hours>** 小时 / **<msgs>** 条消息（深度协作）

## 📂 项目与领域分布

跨 **<n>** 个项目活跃，按领域分布：

| 领域 | 项目数 |
| --- | --- |
| 基础设施 / 部署 | … |
| 产品 / 业务后端 | … |
| ML / 研究 / 论文 | … |
| AI 工具 / Skill | … |
| 数据 / 分析 | … |
| 文档 / Markdown | … |

### Top 项目（脱敏）
| 项目 | Claude sessions | Codex threads | Git commits | 领域 |
| --- | --- | --- | --- | --- |
| 项目 A | 95 | 47 | 188 | 基础设施 / 部署 |
| 项目 B | 42 | 46 | 274 | 其他 |
| … | | | | |

## 💡 兴趣主题 & 关键词

从我的 plan 标题、Codex thread 标题、prompt 文本中自动提取的高频主题：

> **<tag1>** · <tag2> · <tag3> · <tag4> · <tag5> ·  …

（说明：用 2-char 滑窗对中文采样、英文 stem 化后做频次统计；已过滤停用词与明显碎片）

## ⏱️ 工作节奏

### 24 小时活跃热力图
```
00 ▎  12
01 █  32
02 ███▌  85
03 ████████████▌  367
04 ███████  199
05 ███████████▎  339
06 ████████████  350
07 █████████████  379
…
```
（峰值时段 **<H1>~<H2>** 时；说明深度工作落在该窗口）

### 时间跨度
- 首次活跃: **<first>**
- 最近活跃: **<last>**
- 活跃天数: **<active_days>**
- 最长连续活跃: **<streak>** 天
- 单日峰值: **<peak_date>**, **<peak_msgs>** 条消息

## 💰 AI 投入 × 产出

### 每模型 token 总账
| 模型 | spent (新付费) | cache-read (复用) |
| --- | --- | --- |
| Claude Opus 4.6 | … | … |
| Claude Sonnet 4.6 | … | … |
| GPT-5.4 | … | — |
| … | | |

### GitHub 同期产出
- 365 天总贡献: **<calendar_total>**（其中 commits **<n>** / PRs **<n>** / issues **<n>**）
- 拥有仓库总数: **<owned_repos>**

#### Top 仓库（窗口内 commits）
| 仓库 | language | commits | private |
| --- | --- | --- | --- |
| <user>/OpenCMO | Python | 274 | no |
| Private Repo X | TypeScript | 37 | yes |
| … | | | |

### 主要语言（GitHub bytes + 本地 git LOC 合并）
JavaScript / TypeScript · Python · Rust · Go · …

### 单位投入产出（粗略估算）
- 每个本地 commit ≈ **<n>** tokens
- 每行代码（add+del）≈ **<n>** tokens

## 📊 数据来源 & 隐私承诺

- 数据 100% 本地：`~/.claude/*` + `~/.codex/*` + 本地 `git log` + GitHub via `gh` (只查询)
- **未读取任何对话正文**，只用统计字段、计划 / thread 标题、首条用户消息
- 项目名、文件路径已匿名（项目 A/B/C…），API key / token / 邮箱 已正则清洗
- 报告由 Claude Code 按本 skill 自动生成，可重复运行
- 生成时间: **<ISO timestamp>**
```

---

## Step 9 — 输出与交接

把 Markdown 写入 `output/profile_<YYYYMMDD>.md`，
然后给用户**一句话**总结：

```
✅ Profile generated: output/profile_<YYYYMMDD>.md
关键数字：<sessions> sessions / <tokens> tokens / <github_commits> commits
预览：head -40 output/profile_<YYYYMMDD>.md
```

如果用户要求"私人版"，再生成一份 `output/profile_<YYYYMMDD>_private.md`
跳过项目匿名（仍然 scrub 密钥与邮箱）。

---

## 数据缺失时的降级策略

| 缺失项 | 你应该做什么 |
| --- | --- |
| `~/.claude/stats-cache.json` 不存在 | 跳过 Claude 总量章节，仅基于 history.jsonl 估算 |
| `~/.codex/state_5.sqlite` 不存在 | 跳过 Codex 章节；如果 history.jsonl 仍在，至少给个总数 |
| `gh` 未安装 / 未认证 | 跳过 GitHub 章节，profile 仍可生成 |
| 候选路径不是 git 仓库 | 该项目从 git 统计中跳过 |
| 数据全空 | 报告诚实地说明"暂无可统计的本地数据"，不要编数据 |

## 一些务必遵守的红线

- **永远不要** 读取 `~/.claude/projects/*/<id>.jsonl` 文件里的 `message.content` 字段
- **永远不要** 把任何对话原文写进 README
- **永远不要** 联网（除 `gh` 调用 GitHub 自身）
- **永远不要** 修改 `~/.claude` 或 `~/.codex` 下任何文件
- **永远不要** 写脚本替代本指令；本 skill 的本质就是让 agent 自己读、自己算、自己写

## 参考样板（外形上对标这些 skill）

- `~/.claude/skills/deploy/SKILL.md` — 编号步骤 + bash 示例的简洁风格
- `~/.claude/skills/ops-report/` — 只读 sqlite 查询的范式
- `~/.claude/skills/log-patrol/` — 跨数据源汇总并出表格的范式
