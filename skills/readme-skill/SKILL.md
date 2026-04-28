---
name: readme-skill
description: >
  生成一份对外可分享、脱敏的 AI-Native 开发者 README。
  量化展示我对 Claude Code + Codex CLI 的使用深度、AI 协作风格、项目与领域分布、
  兴趣主题，以及与 GitHub 提交的产出关联。
  Trigger when the user says: "生成我的 AI 档案" / "做一份 AI-native README" /
  "分析我的 Claude 使用情况" / "总结我的 AI 使用" / "build my AI usage profile"
  / "summarize my Claude / Codex history" / "生成开发者画像".
  全程本地、只读、默认匿名、不上传任何数据。
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

### 2.4b Skill 清单（AI 基础设施采集）

对每个 skill 目录，读取 `SKILL.md` 的 frontmatter（name + description）：

```bash
for d in ~/.claude/skills/*/; do
  name=$(basename "$d")
  desc=$(head -10 "$d"SKILL.md 2>/dev/null | grep -m1 'description:' | sed 's/.*description: *//')
  echo "$name | $desc"
done
```

同样处理 Codex skills：
```bash
for d in ~/.codex/skills/*/; do
  name=$(basename "$d")
  desc=$(head -10 "$d"SKILL.md 2>/dev/null | grep -m1 'description:' | sed 's/.*description: *//')
  echo "$name | $desc"
done
```

记录每个 skill 是「自建」还是「安装」。如果 skill 目录下有 git remote 指向用户自己的 repo，
标记为自建；否则标记为安装。

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

# --- 以下为 v2.0 新增查询 ---

# 月度聚合（Evolution 曲线用）
$SQ "SELECT strftime('%Y-%m', datetime(created_at,'unixepoch')), COUNT(*), \
     SUM(tokens_used), COALESCE(NULLIF(model,''),'unknown') \
     FROM threads GROUP BY 1,4 ORDER BY 1,3 DESC;"

# CLI 版本时间线（Evolution 曲线用）
$SQ "SELECT cli_version, MIN(date(created_at,'unixepoch','localtime')), \
     MAX(date(created_at,'unixepoch','localtime')), COUNT(*) \
     FROM threads WHERE cli_version != '' GROUP BY 1 ORDER BY 2;"

# 每项目 token 消耗（双工具编排分析用）
$SQ "SELECT cwd, COALESCE(NULLIF(model,''),'unknown'), COUNT(*), SUM(tokens_used) \
     FROM threads WHERE cwd != '' GROUP BY 1,2 ORDER BY 1,4 DESC;"
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

## Step 6 — 计算 10 个维度（你做推理，不要写脚本）

### 6.1 一览
- 总活跃天数 = unique union of all dates from
  `dailyActivity`, codex `by_date`, history `by_date`
- 跨度 = `min..max` of those dates
- 总 sessions / 总消息 / **claude_spent**（Σ input+output+cache_creation）/ **claude_cache_read** / 总 threads / **codex_tokens**（这四个数字必须出现在「一览」里）
- 同期 GitHub: commits, PRs, issues, calendar_total
- 本地 git: commits / +additions / −deletions / repos
- **Velocity 指标**（v2.0 新增）:
  - `commits_per_day = git_local_commits / active_days`
  - `loc_churn_per_day = (additions + deletions) / active_days`
  - `simultaneous_repos = count of repos with ≥1 commit`
  - `cross_stack_langs = count of distinct primary languages across repos`

### 6.2 AI-Native 实践（核心章节）
- **多模型编排**: 列出每个模型的 spent / cache_read tokens
- **高级能力使用**: plan-mode 次数、effort 调节次数、skill 调用次数、
  自研 skills 数、hooks/MCP 数、plans/tasks 数、automations 数
- **Prompt caching 熟练度**: cache_to_spent_ratio
- **Reasoning effort 分布**: xhigh/high/medium/low 占比
- **AI 基础设施层**（v2.0 新增 —— 这是最 AI-native 的信号）:
  列出用户亲手构建的 skills / hooks / automations / rules，每项给
  `名称 | 一句话描述 | 调用次数（如可从 history 统计）`。
  区分「自建」（用户原创）与「安装」（第三方）。
  这一段的叙事重点：**不只是 AI 的使用者，更是 AI 工作流的建设者**。

### 6.3 协作风格
- Top 10 slash commands (cmd, count, 简短解读)
- Plan-to-direct ratio = `/plan count / non-command prompt count`
- 平均消息/session = `totalMessages / totalSessions`
- 最长 session: 时长(小时) + 消息数
- **Session 架构**（v2.0 新增）:
  从 history.jsonl 中按 sessionId 分组，统计典型 session 内的命令序列模式：
  - 以 `/plan` 开头的 session 占比 → 说明「先想再做」的习惯有多强
  - session 内使用 `/compact` 或 `/clear` 的比例 → 上下文管理意识
  - `/effort` 在 session 内的切换频率 → 是否按阶段调节推理深度
  - `/resume` 使用率 = resume_count / totalSessions → session 连续性
  用 2-3 句话总结出用户的 **session 驾驭模式**（例如：
  「典型流程：/plan 规划 → 迭代 → /compact 回收上下文 → 继续交付」）

### 6.4 项目与领域
- 合并维度: each project key (real_cwd) accumulates
  `sessions`(claude) + `codex_threads` + `git_commits` + `git_lines`
- 综合分数 = `sessions*5 + codex_threads*4 + git_commits`
- 排序取 Top 12，匿名化为 "项目 A/B/C..."（按分数顺序）
- **双工具编排模式**（v2.0 新增）：对每个 Top 12 项目，计算：
  - `claude_ratio = claude_sessions / (claude_sessions + codex_threads)`
  - 分类为：**Claude 主导**（ratio > 0.7）/ **Codex 主导**（ratio < 0.3）/ **双引擎**（0.3~0.7）
  - 在项目表中新增「编排模式」列
  - 汇总：双引擎项目数 / Claude 主导数 / Codex 主导数
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

### 6.7 投入 × 产出
- 每模型 spent / cache_read 表（**降级展示**：放在折叠区或尾部，不再作为核心亮点）
- GitHub 1 年贡献日历（求 calendar_total，列出 top 5 高产日）
- Top GitHub 仓库（commits 排序；private 仓库改名为 "Private Repo X"）
- 主要语言：merge `gh languages.bytes` 与本地 `git numstat ext` 排序
- **产出密度**（v2.0 重点，替代原来的 tokens_per_commit）:
  - `commits_per_day = git_local_commits / active_days`
  - `loc_churn_per_day = (additions + deletions) / active_days`
  - `github_contribs_per_active_day = calendar_total / active_days`
  - 贡献爆发日：连续 3+ 天 daily_contributions > 20 的窗口
- 单位投入产出（`tokens_per_commit` / `tokens_per_loc`）已迁移到 **6.10 Token 经济学**，6.7 只讲 GitHub / 仓库 / 语言

### 6.8 Velocity & Leverage（v2.0 新增 —— AI 让你快了多少、广了多少）

这一维度的目的是回答：**如果没有 AI 协作，这种产出可能吗？**

计算并叙述：
- **日均产出**: commits/day, LOC churn/day, GitHub contributions/day
- **跨栈广度**: 同时活跃的仓库数 × 使用的编程语言数。
  一个人用 Python + TypeScript + Rust + Go + Shell 跨 13 个仓库日均 10 commit，
  这种广度只有 AI 辅助才现实。
- **同时在线项目数**: 活跃天数 >= 3 的项目数量
- **开源影响力**: 总 stars × repos with stars > 0 → 产出不只是 "量"，还被社区认可
- 用 1-2 句总结性叙事，例如：
  「AI 让一个人拥有了小团队的交付能力：13 个仓库、5 门语言、日均 10 commit。」

### 6.9 Evolution 曲线（v2.0 新增 —— 你的 AI 用法在进化）

目的：把静态快照变成成长叙事。让读者看到 **AI 使用的成熟度曲线**。

数据来源：
- Codex `threads` 的 `created_at` + `model` + `cli_version`
- Claude `history.jsonl` 的 `timestamp` + `display`（斜杠命令）
- Claude `projects/` 目录的 JSONL 文件创建时间

计算：
1. **月度活跃量**：每月 Claude sessions + Codex threads
2. **能力解锁时间线**（从 history.jsonl 提取各能力的首次使用日期）：
   - 首次 `/plan` 的日期 → Plan-mode 解锁
   - 首次 `/effort` 的日期 → Reasoning effort 解锁
   - 首次 `/skill-creator` 或自研 skill 出现的日期 → Skill 自建解锁
   - 首次 `/vibe-forge` 或 `/ssh-prod` 的日期 → 自建 skill 投入生产
   - Codex CLI 版本跳跃点（major version changes）
3. **模型迁移**：从 Codex 月度模型聚合中，标出何时从旧模型迁移到新模型

渲染为 timeline 格式：
```
2026-01  Codex 起步，纯 prompt，CLI 0.81.0-alpha
2026-02  开始日常化，tokens 增长
2026-03  Claude Code 加入 → plan-mode + effort 调节 → 开始自建 skills
2026-04  双工具编排成熟，skills 生态完善，日均 10 commit
```

### 6.10 💎 Token 经济学（v2.1 新增 —— 把 token 投入当成"AI 投资"来叙事）

目的：不只展示"用了多少 token"，而是讲清 **token 投入怎么花、Cache leverage 多深、模型迁移如何省了成本**。把 token 当 AI 时代的"原材料 + 杠杆"来叙事，不是产出的注脚。

数据来源：
- Claude `stats-cache.json` 的 `modelUsage` + `dailyModelTokens`
- Codex sqlite `threads` 的 `tokens_used` + 月度聚合（Step 3.1 已查）

计算：
1. **总投入** = `claude_spent + codex_tokens`（新付费 token 总量）
2. **总杠杆** = `claude_cache_read`（缓存复用 token 总量）
3. **Cache leverage 倍率** = `claude_cache_read / claude_spent`（每 1 个新 token 撬动几个缓存 token）
4. **每模型占比** = `model.spent / Σ all_models.spent`（Claude 与 Codex 合在一起算）
5. **每模型 leverage** = `model.cache_read / model.spent`（哪个模型 caching 习惯最熟）
6. **月度 token 趋势** = `dailyModelTokens` + Codex 月度聚合按月汇总，找增长拐点
7. **模型迁移注解** = 比较相邻两个月每模型 spent 增减，识别"萎缩-接管"事件
   （例如 `<YYYY-MM>: Opus 4.6 spent ↓ 40%，Sonnet 4.6 spent ↑ 60%`）
8. **单位投入产出**（从原 6.7 移过来，仅参考）= `claude_spent / git_commits` 与 `claude_spent / (additions + deletions)`

叙事重点：
- 一句话开场：「**<X>** 新付费 token 撬动 **<Y>** 缓存复用，杠杆比 **1 : <N>**」
- 月度趋势用文字 sparkline 或表格
- 模型迁移注解：哪些月份发生了什么、为什么省/费 token
- 单位投入产出标注为"参考"，提醒不要当 KPI

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
(中文叙事 + 表格；技术词保留英文；**讲故事优先于堆数据**；
展示「因为 AI 而不同」，而不仅仅是「用了很多 AI」)：

```markdown
# <name or github_login> · AI-Native Developer Profile
> 基于 <span_days> 天的本地 Claude Code + Codex 对话数据自动生成 · <generated_at>
> _个人理念：<github bio if available>_

---

## 一览

- 在 **<span_days>** 天里完成 **<claude_sessions>** 次 Claude sessions + **<codex_threads>** 次 Codex threads，共 **<total_messages>** 条消息
- 日均产出：**<commits_per_day>** commits / **<loc_churn_per_day>** 行代码变动 / **<github_contribs_per_day>** GitHub contributions
- 同时维护 **<git_repos>** 个仓库，横跨 **<cross_stack_langs>** 门语言
- 同期 GitHub：**<github_commits>** commits / **<github_prs>** PRs / **<github_issues>** issues / **<calendar_total>** 总贡献
- AI 投入：**<claude_spent>** Claude 新付费 token + **<codex_tokens>** Codex token；复用 **<claude_cache_read>** 缓存（占 Claude I/O **<cache_pct>%**）
- 主力工具：Claude Code (Opus 4.6 + Sonnet 4.6) + Codex CLI (GPT-5.4)

## 🚀 Velocity & Leverage — AI 让一个人拥有了小团队的交付能力

> <1-2 句叙事，例如：「13 个仓库、5 门语言、日均 10 commit —— 这种跨栈广度和交付密度，只有 AI 协作才现实。」>

| 指标 | 数值 | 说明 |
| --- | ---: | --- |
| 日均 commits | <n> | 本地 git commits / 活跃天数 |
| 日均代码变动 | <n> 行 | (additions + deletions) / 活跃天数 |
| 同时维护仓库 | <n> 个 | 过去一年有 commit 的仓库数 |
| 跨栈语言 | <n> 门 | Python / TypeScript / Rust / Go / … |
| GitHub 贡献爆发 | <dates> | 连续 3+ 天 daily > 20 的窗口 |
| 开源影响力 | <total_stars> stars | 跨 <n> 个被 star 的仓库 |

## 🤖 AI-Native 实践

> 不是「偶尔问问 AI」，是把多 LLM 编排、planning、structured workflows 都跑通。

### 多模型编排
| 模型 | sessions / threads | spent tokens | cache-read | 用途倾向 |
| --- | ---: | ---: | ---: | --- |
| Claude Opus 4.6 | … | … | … | 深度推理、复杂规划 |
| Claude Sonnet 4.6 | … | … | … | 快速迭代、批量任务 |
| GPT-5.4 (Codex) | … | … | — | 第二意见、跨工具诊断 |
| … | | | | |

### 高级能力深度使用
- **Plan-mode**: **<n>** 次
- **Effort 调节**: **<n>** 次
- **Skills**: 共 **<n>** 个（Claude <n> + Codex <m>）
- **Plans**: **<n>** 份；Tasks: **<n>** 个
- **Hooks**: **<n>** 个；Automations: **<n>** 个

### Prompt caching 熟练度
每花费 1 个新 token，复用 **<ratio>** 个缓存 token（cache-read 占总 IO 的 **<%>**）。

### Reasoning effort 偏好
xhigh **<n>**（**<%>**）· high **<n>** · medium **<n>** · low **<n>**

## 🔧 AI 基础设施 — 不只用 AI，还在给 AI 造工具

> <1 句叙事：「从 skill 到 hook 到 automation，我在构建让 AI 更好地帮我工作的基础设施。」>

### 自建 Skills
| 名称 | 描述 | 调用次数 | 工具 |
| --- | --- | ---: | --- |
| <skill_name> | <一句话> | <n> | Claude / Codex |
| … | | | |

### 安装的 Skills
| 名称 | 描述 | 工具 |
| --- | --- | --- |
| … | | |

### 其他基础设施
- Hooks: <列出>
- Codex automations: <列出>
- Codex rules: <列出>

## 🛠️ AI 协作风格

### 最常用的 slash 命令 Top 10
| # | 命令 | 次数 | 含义 |
| --- | --- | ---: | --- |
| 1 | /effort | <n> | 切换推理深度 |
| 2 | … | … | … |

### Session 架构
<2-3 句描述用户的 session 驾驭模式，例如：>
- 典型流程：`/plan` 规划 → 深度迭代 → `/compact` 回收上下文 → 继续交付
- **<n>%** 的 session 以 `/plan` 开头（先想再做）
- **<n>%** 的 session 使用过 `/compact` 或 `/clear`（主动管理上下文）
- `/resume` 恢复率: **<n>%**（session 连续性）
- 平均会话深度: **<n>** 条消息 / session
- 最长 session: **<hours>** 小时 / **<msgs>** 条消息

## 📂 项目与领域分布

跨 **<n>** 个项目活跃，按领域分布：

| 领域 | 项目数 |
| --- | ---: |
| … | … |

### Top 项目（脱敏）
| 项目 | Claude | Codex | Git commits | 编排模式 | 领域 |
| --- | ---: | ---: | ---: | --- | --- |
| 项目 A | <n> | <n> | <n> | 双引擎 | … |
| 项目 B | <n> | <n> | <n> | Codex 主导 | … |
| … | | | | | |

编排模式统计：双引擎 **<n>** 个 · Claude 主导 **<n>** 个 · Codex 主导 **<n>** 个

## 🧬 Evolution 曲线 — AI 用法在进化

```
<YYYY-MM>  <里程碑事件>
<YYYY-MM>  <里程碑事件>
<YYYY-MM>  <里程碑事件>
<YYYY-MM>  <里程碑事件>
```

月度活跃趋势：
| 月份 | Claude sessions | Codex threads | 里程碑 |
| --- | ---: | ---: | --- |
| … | | | |

## 💡 兴趣主题 & 关键词

> **<tag1>** · <tag2> · <tag3> · … （top 25，已过滤停用词）

## ⏱️ 工作节奏

### 24 小时活跃热力图
```
00 …
01 …
…
```
（峰值时段 / 活跃模式描述）

### 时间跨度
- 首次 / 最近活跃: …
- 活跃天数 / 最长连续 / 单日峰值: …

## 💎 Token 经济学

> 一句叙事开场：「**<spent_tokens>** 新付费 token 撬动 **<cache_read>** 缓存复用，杠杆比 **1 : <leverage>**；总通过我手里 **<total_tokens>** token。」

### 每模型 token 明细（按 spent 排序）
| 模型 | spent | cache-read | leverage | 占总 spent |
| --- | ---: | ---: | ---: | ---: |
| Claude Opus 4.6 | … | … | …× | …% |
| Claude Sonnet 4.6 | … | … | …× | …% |
| Claude Haiku 4.5 | … | … | …× | …% |
| GPT-5.4 (Codex) | … | — | — | …% |
| … | | | | |

### 月度 token 趋势
| 月份 | Claude spent | Claude cache | Codex tokens | 主力模型 | 注解 |
| --- | ---: | ---: | ---: | --- | --- |
| <YYYY-MM> | <n> | <n> | <n> | <model> | <事件 / 迁移> |

### 模型迁移注解
- <YYYY-MM>: <模型 A 萎缩 −X%>，<模型 B 接管 +Y%>，<推测原因>
- …

### 单位投入产出（仅参考，勿当 KPI）
- 每 commit ≈ **<n>** Claude tokens（仅 spent，不含 cache 与 Codex）
- 每行代码 ≈ **<n>** Claude tokens
> 提醒：AI 产出还包含大量不直接转化为 commit 的高价值劳动（架构 review / 数据清洗 / plan 推演 / skill 重构）。把"每 commit X tokens"当 KPI 是反激励。

## 💰 产出 & 投入

### GitHub 同期产出
- 365 天总贡献: **<n>** · 拥有仓库: **<n>**
- 最高产单日: <top 5 dates>

#### Top 仓库
| 仓库 | language | commits | stars |
| --- | --- | ---: | ---: |
| … | | | |

### 主要语言
<语言列表>

## 📊 数据来源 & 隐私承诺

- 数据 100% 本地：`~/.claude/*` + `~/.codex/*` + 本地 `git log` + GitHub via `gh`
- 对话正文仅用于关键词与协作风格分析，原文不会出现在报告中
- 项目名已匿名，API key / token / 邮箱 已正则清洗
- 报告由 Claude Code / Codex 按 Readme.skill 自动生成，可重复运行
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

- 可以读取 `~/.claude/projects/*/<id>.jsonl` 里的 `message.content` 用于关键词提取、协作风格、Session 架构等深度分析（Step 6.3 / 6.5 受益）；但**不要把任何对话原文一字不差地写进 README**——脱敏后的统计、概括、片段化关键词可以
- **永远不要** 联网（除 `gh` 调用 GitHub 自身）
- **永远不要** 修改 `~/.claude` 或 `~/.codex` 下任何文件
- **永远不要** 写脚本替代本指令；本 skill 的本质就是让 agent 自己读、自己算、自己写

## 参考样板（外形上对标这些 skill）

- `~/.claude/skills/deploy/SKILL.md` — 编号步骤 + bash 示例的简洁风格
- `~/.claude/skills/ops-report/` — 只读 sqlite 查询的范式
- `~/.claude/skills/log-patrol/` — 跨数据源汇总并出表格的范式
