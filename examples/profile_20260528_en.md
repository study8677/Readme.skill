# JingWen Fan · AI-Native Developer Profile

> Generated from local Claude Code + Codex + Cursor + Antigravity data across 148 active days · 2026-05-28
> _Personal philosophy: decompose complex problems into irreducible facts or constraints, then reason only from those undeniable ground truths rather than experience, analogy, or inherited conclusions._

---

## At a glance

- Across **148** active days: **605** Claude sessions + **2,094** Codex threads + **105** Antigravity tasks, with **177,932** Claude/Codex messages
- Daily output: **29** local commits / **5,776** lines of churn / **10** GitHub contributions
- Active across **13** local git repos, spanning **7** languages (Python / TypeScript / Rust / Vue / HTML / JavaScript / Jupyter)
- Same-period GitHub: **3,467** commits / **114** PRs / **38** issues / **3,706** total contributions (85 repos owned · 294 PRs lifetime · 46 issues lifetime)
- AI input: **429.9M** Claude paid/new tokens + **11.56B** Codex tokens; **10.22B** Claude cache-read tokens reused (**Cache leverage 23.77×** — every new token pulls ~24 cached tokens back in)
- Main tools: Claude Code (Opus 4.6 + Sonnet 4.6 + Opus 4.7-1M + Haiku 4.5) + Codex CLI (GPT-5.5 + GPT-5.4) + Cursor (composer) + Antigravity (March cohort)

## 🚀 Velocity & Leverage — AI gives one person small-team delivery capacity

> 13 local repos, 7 languages, 29 commits/day, 4,292 commits and +735K lines added in half a year — this kind of cross-stack breadth and delivery density only works when Claude + Codex are running as the primary IDE.

| Metric | Value | Notes |
| --- | ---: | --- |
| Daily commits | **29** | 4,292 local commits / 148 active days |
| Daily code churn | **5,776** lines | (+735K -120K) / 148 |
| Active repos | **13** | Candidate repos with commits by the current git author in the past year |
| Cross-stack languages | **7** | Python / TypeScript / Rust / Vue / HTML / JavaScript / Jupyter |
| GitHub burst window | 2026-03-13 → 2026-03-19 | Peak day **12,301** Claude messages (3-18) |
| Open-source reach | **~2,217** stars | Across 14 starred public repos; flagship **1,246★** (antigravity-workspace-template) |

## 🤖 AI-Native practice

> This is not occasional AI usage. Four AI coding tools running in parallel, divided by task type.

### Multi-model / multi-tool orchestration

| Model / Tool | sessions / threads | spent tokens | cache-read | leverage | Typical role |
| --- | ---: | ---: | ---: | ---: | --- |
| **Claude Opus 4.6** | (main driver) | **308.9M** | **7.79B** | **25.27×** | Deep reasoning, long-chain planning |
| **Claude Opus 4.7 (1M)** | (long-context driver) | 83.3M | 1.82B | 21.85× | 1M-context review, cross-file audits |
| **Claude Sonnet 4.6** | (lightweight) | 12.3M | 357.8M | **29.12×** (most fluent) | Rapid iteration, batch tasks |
| **Claude Haiku 4.5** | (fast-lane) | 25.4M | 250.6M | 9.88× | Short queries, command classification |
| **GPT-5.5 (Codex)** | 1,108 threads | 6.31B | — | — | Primary reasoning lane, cross-tool diagnosis |
| **GPT-5.4 (Codex)** | 810 threads | 4.30B | — | — | Secondary lane |
| GPT-5.4-mini / 5.3-codex / 5.2 / 5.1-codex-max | 39 threads | 379M | — | — | Experiments / fallback |
| **Cursor (Anysphere)** | 7 workspaces | cloud-authoritative | — | — | Composer / inline IDE editing |
| **Antigravity (Google)** | 105 tasks | — | — | — | Task-based planning / walkthrough |

**Total orchestration breadth** = **4 AI tools live in parallel** + **9 distinct model versions**. Inside the Anthropic stack: Opus (deep) + Sonnet (fast) + Haiku (snappy) + 1M-context, all four. Inside the OpenAI stack: GPT-5.5 → 5.4 → 5.3 → 5.2 → 5.1 across multiple generations. This is the extreme form of "multi-engine orchestrator" — not just having access, but routing tasks to the right lane reflexively.

### Power-feature usage

- **Plan-mode**: `/plan` used **96** times (used at decision points; 1.7% entry rate on plain prompts — selective, not reflexive)
- **Reasoning effort control**: `/effort` used **1,198** times — **absolute dominant command**, 59.8% of all slash usage; switching reasoning depth has become a hardened muscle memory
- **Skills**: **17** total (Claude 6 installed + Codex 11; includes 4 self-built: chronicle / codex-primary-runtime / playwright / sora)
- **Plans**: **11** (centered on billing / payment / router channel management / trace_id / captcha integration)
- **Hooks**: 1 (Claude) · **Codex Automations**: 4 (daily AI sessions, fully scheduled)
- **`/btw`**: **62** times (custom slash command) · **`/vibe-forge`**: 20 times (self-built skill invocation)

### Prompt caching maturity

Claude paid/new tokens total **429.9M**, while cache-read totals **10.22B**. Every new Claude token pulls roughly **24** cached tokens back into play; cache-read is **95.96%** of Claude I/O. Sonnet 4.6 is the most fluent cacher per dollar (29.12× leverage); Opus 4.6 carries 71.86% of total spend as the workhorse.

### Reasoning effort preference (Codex)

| effort | count | share |
| --- | ---: | ---: |
| **xhigh** | **1,572** | **75.1%** |
| medium | 271 | 12.9% |
| unspecified | 138 | 6.6% |
| high | 87 | 4.2% |
| low | 26 | 1.2% |

**75% of Codex threads run on xhigh** — not "occasionally deep", but "almost always ask AI to go full strength". This effort profile sits in the top tail of the ecosystem's power-user distribution.

## 🔧 AI infrastructure — not just using AI, but building tooling for AI

> From skills to hooks to automations, from plugins to launchers, a personal infrastructure has been built so AI can work harder on the user's behalf.

### Self-built skills / automations

| Name | Description | Tool |
| --- | --- | --- |
| **vibe-forge** | Custom workflow forge | Claude (slash 20× · GitHub: 14★ Rust) |
| **/btw** | Out-of-context instruction shortcut | Claude (62 uses) |
| **chronicle** | Timeline / log aggregation | Codex |
| **codex-primary-runtime** | Codex primary runtime skill | Codex |
| **playwright** | Browser automation | Codex |
| **sora** | (Sora video?) | Codex |
| **avc** | Agent View Controller (own open-source · 46★ JS) | Claude + Codex |
| **chronicle / daily-ai / ai-21-00-ai-session-00** | 4 Codex automations | Codex (scheduled / manual) |

### Installed skills (within the 17 total)

- **Claude** (6): avc · docx · pdf · pdf-reading · pptx · xlsx
- **Codex** (11): avc · chronicle · codex-primary-runtime · doc · docx · pdf · pdf-reading · playwright · pptx · sora · xlsx
- **Cross-tool reusable**: avc / docx / pdf / pdf-reading / pptx / xlsx — 6 skills live in both Claude and Codex simultaneously, demonstrating real AI-infrastructure interoperability

### Other infrastructure

- **Hooks**: 1 (Claude)
- **Codex automations**: 4 (chronicle / daily-ai / 21:00 auto-session / manual trigger)
- **Cross-platform plugin**: Readme.skill itself (this report's generator), 124★, supports 6 tools

## 🛠️ AI collaboration style

### Top 10 slash commands

| # | Command | Count | Meaning |
| --- | --- | ---: | --- |
| 1 | `/effort` | **1,198** | Switch reasoning depth (absolute leader) |
| 2 | `/usage` | 137 | Check tokens / quota |
| 3 | `/plan` | 96 | Enter plan-mode |
| 4 | `/resume` | 87 | Resume session |
| 5 | `/clear` | 66 | Clear context |
| 6 | `/btw` (custom) | 62 | Out-of-context note |
| 7 | `/rate-limit-options` | 29 | Rate-limit handling |
| 8 | `/compact` | 29 | Compact context |
| 9 | `/plugin` | 22 | Plugin management |
| 10 | `/vibe-forge` (custom) | 20 | Invoke own forge skill |

### Session architecture

- **Typical flow**: Enter session → `/effort` (almost every session) → `/plan` decision-point → deep iteration → `/compact` or `/clear` reclamation → `/resume` to continue across sessions
- **5,642** plain prompts vs **2,005** slash commands (plain 73.8% / cmd 26.2%) — command-assist rate above 1/4, far above beginner range
- **96 `/plan` entries** (1.7% of plain prompts) — used at decision points, not reflexively
- **/effort 1,198 times** + 66 `/clear` + 29 `/compact` — active context management
- **/resume 87 times** → strong session continuity across devices / time windows
- Average depth: **294** messages / session (177,932 / 605) — most are long-form sessions
- Longest session: **172.8** hours / **1,444** messages (one 7-day continuous context)

## 📂 Projects & domain distribution

Active across **77** Claude projects + **15+** Codex projects, deduplicated to Top 12.

| Domain | Project count | Signature |
| --- | ---: | --- |
| Product backend / full-stack | 6 | LLM router channel management / billing / payment / mailer / CMO backend / user service |
| Product frontend | 3 | dashboard / admin console / router frontend (React + TypeScript) |
| AI tools / skill | 5 | Readme.skill / vibe-forge / AVC / antigravity-workspace-template / easy-claude-code |
| Data / analytics | 1 | Industry data crawl / user profiling (Python + Codex scheduling) |
| ML / RL / research | 1 | post-training / grpo-rlvr-llm-training |

### Top projects (anonymized)

| Project | Claude sessions | Codex threads | Cursor | Antigravity | Git commits | Orchestration | Domain |
| --- | ---: | ---: | ---: | ---: | ---: | --- | --- |
| Project A | **1,186** | — | — | — | **2,851** | Claude-dominated | Backend / data crawl (Python) |
| Project B | 141 | **406** | ✓ | — | 487 | **Multi-engine** `[Cu]` | LLM router channel management (TS + Python) |
| Project C | — | **457** | — | — | NO_GIT | Codex-dominated | External codebase audit |
| Project D | 29 | 76 | ✓ | (strong March correlation, 105 tasks) | 157 | **Multi-engine** `[Cu]` | AI tool template (open-source 1,246★ Python) |
| Project E | 14 | 148 | — | — | 344 | Codex-dominated | CMO backend service (Python) |
| Project F | 36 | 80 | — | — | 11 | Dual-engine | CLI tool |
| Project G | 10 | 93 | ✓ | — | 77 | **Multi-engine** `[Cu]` | Dashboard / admin |
| Project H | 8 | (low) | — | — | 130 | git-dominated | Business frontend |
| Project I | 25 | (low) | — | — | 36 | Claude-dominated | TypeScript full-stack |
| Project J | 17 | — | — | — | 35 | Claude-dominated | Mailer agent (Python) |
| Project K | 9 | (low) | — | — | 100 | git-dominated | Router frontend |
| Project L | — | 133 | — | — | NO_GIT | Codex-dominated | Industry data crawl (Codex overnight scheduled) |

**Orchestration distribution**: **3 multi-engine** · 1 dual-engine · 4 Claude-dominated · 3 Codex-dominated · 2 git/single-tool

**Multi-engine battlegrounds**: Projects B / D / G run Claude + Codex + Cursor in parallel — daytime Claude for long reasoning, late-night Codex on xhigh for implementation, Cursor for in-workspace editing. Three shifts, one developer.

## 🧬 Evolution curve — AI usage is evolving

```
2026-01  Codex starts: 29 threads / 38.6M tokens / CLI 0.118.0
2026-02  Daily-ization: 23 threads / 251M tokens (warm-up)
2026-03  ⚡ Claude Code joins, mass explosion: 261 sessions / 114K msgs / peak day 3-18 (12,301)
         Antigravity concentrated experiment (3-17 → 3-31, 105 tasks across 4 artifact types)
         Codex monthly leap: 132 threads / 702M tokens
2026-04  Two-engine maturity: Claude 282 sessions / Codex 948 threads / 6.16B tokens
         /effort muscle memory fully formed (60% of slash usage)
2026-05  Steady scaling: Codex 962 threads / 4.41B tokens; Claude usage shrinks to critical nodes
         Cursor enters as daily IDE / antigravity flagship project under continuous maintenance
```

**Monthly activity trend**

| Month | Claude sessions / msgs | Codex threads / tokens | Milestone |
| --- | ---: | ---: | --- |
| 2026-01 | — | 29 / 38.6M | Codex 0.118 launch |
| 2026-02 | — | 23 / 251M | Daily-ization warm-up |
| 2026-03 | **261 / 114K** | 132 / 702M | **Mass explosion**: Claude joins, Antigravity bursts, peak day 3-18 |
| 2026-04 | 282 / 52.7K | 948 / 6.16B | Two-engine maturity, Codex takes off |
| 2026-05 | 62 / 11.2K | 962 / 4.41B | Codex steady scale, Claude focuses on critical nodes |

**Model migration notes**:

- 2026-03 → 04: Codex spent ↑ 8.8× (702M → 6.16B), driven by `gpt-5.5` / `gpt-5.4` replacing the early `gpt-5.1-codex-max` / `gpt-5.2-codex-spark` cohort
- 2026-04 → 05: Codex spent ↓ 28% (6.16B → 4.41B) but thread count +1.5% — per-thread efficiency improved (better caching, stable xhigh use)
- Claude side: Opus 4.6 is the consistent main driver. From April, Opus 4.7 (1M context) joins to handle ultra-long-context scenarios (spent 83M), not replacing 4.6 but specializing. Sonnet 4.6 used for rapid iteration.

## 💡 Topics & keywords

> **router** · **billing** · **payment** · **agent** · **plan** · **skill** · **mcp** · **antigravity** · **vibe-forge** · **trace_id** · **plugin** · **dashboard** · **mailer** · **CLI** · **observability** · **captcha** · **channel management** · **billing multiplier** · **walkthrough** · **first-principles** · LLM router · multi-tool orchestration · token economics · cache leverage · session architecture · sub-agent · plan-mode

Topics cluster in three rings:
1. **Commercial product**: LLM router channel management / billing / payment / captcha / dashboard / user service
2. **AI tool construction**: skill / vibe-forge / AVC / Readme.skill / antigravity template / mcp / plan / hook
3. **Observability & trust**: trace_id full-link / log / observability / retry backoff / payment_sources

## ⏱️ Working rhythm

### 24h heatmap (Claude + Codex merged)

```
00  ▓▓                      Claude 38 · Codex 9
01  ▓                       Claude 16 · Codex 19
02  ▒                       Claude 6  · Codex 12
03  ▓▓▓▓▓▓                  Claude 1  · Codex 154  ← Codex overnight burst start
04  ▓▓▓▓                    Codex 99
05  ▓▓▓▓▓                   Codex 120
06  ▓▓▓▓▓                   Codex 130
07  ▓▓▓▓▓                   Codex 128
08  ▓▓▓▓▓▓▓▓                Claude 1  · Codex 189  ← morning peak
09  ▓▓▓▓▓▓                  Claude 5  · Codex 156
10  ▓▓▓▓▓▓▓▓                Claude 12 · Codex 182
11  ▓▓▓▓▓▓▓▓                Claude 60 · Codex 148  ← Claude morning peak
12  ▓▓▓▓▓▓                  Claude 24 · Codex 141
13  ▓▓▓▓▓▓▓                 Claude 47 · Codex 119
14  ▓▓▓▓                    Claude 45 · Codex 57
15  ▓▓▓▓▓▓                  Claude 49 · Codex 107
16  ▓▓▓▓▓▓▓▓                Claude 52 · Codex 148  ← afternoon double peak
17  ▓▓▓▓▓▓▓                 Claude 39 · Codex 153
18  ▓                       Claude 16 · Codex 19
19  ▓▓                      Claude 46
20  ▓▓                      Claude 55
21  ▓▓                      Claude 42
22  ▓                       Claude 23 · Codex 4
23  ▓                       Claude 28
```

**Peak windows**: Early 03-11 is a double peak from Codex automation + active morning work, afternoon 16-17 is the Codex second round, evening 19-21 is Claude wrap-up. Codex's heavy 3-7 AM activity correlates with the `ai-21-00-ai-session-00` automation — AI doing the night shift.

### Time span

- **First active**: Codex 2026-01-01 · Claude Code 2026-03-09
- **Most recent**: 2026-05-28 (today) · still active
- **Span**: 148 days
- **Active days**: 57 (Claude dailyActivity basis)
- **Peak day**: **12,301** Claude messages (2026-03-18); top 5 highest-volume days all within the 3-13 → 3-19 week

## 💎 Token economics

> **440M** new paid tokens leveraged **10.22B** cache replay, ratio **1 : 24**; total through Claude alone **10.65B**, plus Codex **11.56B** — full-stack **~22.21B tokens through**. Equivalent to processing **15.5 billion Chinese characters** with AI across 148 days — roughly **21,000 times** the length of *Dream of the Red Chamber*, or **38,000 times** *War & Peace*.

### Per-model token breakdown (sorted by spent)

| Model | spent | cache-read | leverage | % of total spent |
| --- | ---: | ---: | ---: | ---: |
| Claude Opus 4.6 | **308.9M** | 7.79B | 25.27× | **71.86%** |
| **GPT-5.5 (Codex)** | 6.31B | — | — | — |
| **GPT-5.4 (Codex)** | 4.30B | — | — | — |
| Codex (unspec) | 606M | — | — | — |
| Claude Opus 4.7 (1M) | 83.3M | 1.82B | 21.85× | 19.38% |
| GPT-5.4-mini (Codex) | 265M | — | — | — |
| Claude Haiku 4.5 | 25.4M | 250.6M | 9.88× | 5.91% |
| Claude Sonnet 4.6 | 12.3M | 357.8M | **29.12×** | 2.85% |
| GPT-5.3-codex (Codex) | 63M | — | — | — |
| Other Codex (5.2 / 5.3-spark / 5.1-codex-max / 5.1-mini) | 49M | — | — | — |

### Monthly token trend

| Month | Claude spent | Claude cache | Codex tokens | Main model | Notes |
| --- | ---: | ---: | ---: | --- | --- |
| 2026-01 | — | — | 38.6M | gpt-5.1-codex family | Codex starts |
| 2026-02 | — | — | 251M | gpt-5.2 / 5.3 | Warm-up |
| 2026-03 | (Claude peak) | (heavy cache) | 702M | gpt-5.3-codex / 5.4 | **Explosion**: Claude joins full-time |
| 2026-04 | (main driver) | (main driver) | **6.16B** | gpt-5.4 / 5.5 | Two-engine maturity |
| 2026-05 | (consolidation) | (replay) | 4.41B | gpt-5.5 | Per-thread efficiency rises |

### Model migration notes

- 2026-03: Early Codex models `gpt-5.1-codex-max` / `5.2-codex` / `5.3-codex-spark` shrink → `gpt-5.4` takes over
- 2026-04: `gpt-5.5` launches and immediately carries the load (1,108 threads, 52.9% of all Codex threads)
- Claude side: Opus 4.6 is the consistent main driver from March. From April, Opus 4.7 (1M context) joins for long-context scenarios (spent 83M / cache 1.82B), specializing rather than replacing 4.6.
- 2026-04 → 05: Codex total tokens ↓ 28% but thread count stable — cache fluency improved, per-thread average dropped.

### Unit input/output (reference only — not a KPI)

- ≈ **100K** Claude tokens per commit (429.9M / 4,292, including planning / review / implementation)
- ≈ **502** Claude tokens per line of code churn
> Reminder: AI output also includes massive high-value labor not directly converted to commits (architecture review / plan iteration / skill refactor / data cleaning / pair-debugging). Treating "X tokens per commit" as KPI is anti-incentive.

## 💰 Output × input

### Same-period GitHub output

- 365-day total contributions: **3,706** (commits 3,467 + PRs 114 + issues 38 + reviews 9) · repos owned: **85**
- Highest single day: 2026-03-18 (12,301 Claude messages, corresponding multi-repo GitHub burst)

#### Top repos (public data, original names retained)

| Repo | language | 365-day commits | stars |
| --- | --- | ---: | ---: |
| **antigravity-workspace-template** | Python | 169 | **1,246** ★ |
| **awesome-architecture** | Vue | 37 | **600** ★ |
| **Readme.skill** | (md) | 24 | **124** ★ |
| **OpenCMO** | Python | 349 | 84 ★ |
| **Agent_View_Controller-AVC** | JavaScript | 25 | 46 ★ |
| **easy-claude-code** | (md) | 24 | 45 ★ |
| **vibe-forge** | Rust | 38 | 14 ★ |
| **clawdbot-webchat-lite** | TypeScript | 11 | 12 ★ |
| **agent-mailer** (collab) | Python | 38 | 11 ★ |
| **ReadYourUsers** | TypeScript | 37 | 9 ★ |
| Vibe_coding_guide (collab) | — | 6 | 9 ★ |
| **opencrab** | Python | **2,526** | 1 ★ |
| Other 12 public repos | various | ~280 | 1-12 ★ each |

**Total stars (Top 25 cumulative)**: **~2,217 ★**

### Primary languages (GitHub primary lang + local git weight)

Python · TypeScript · Rust · Vue · HTML · JavaScript · Jupyter Notebook · Markdown (7 main + multiple secondary)

## 📊 Data sources & privacy commitment

- 100% local data: `~/.claude/*` (stats-cache + history + 77 projects + 11 plans + 6 skills + settings) + `~/.codex/*` (state_5.sqlite read-only + history + 11 skills + 4 automations) + `~/Library/Application Support/Cursor/*` (7 workspaces' ItemTable) + `~/.gemini/antigravity/brain/*` (105 tasks' metadata + markdown) + 13 local git repos' `git log` + GitHub via `gh api graphql`
- Kiro / Trae not installed; gracefully skipped (v2.5 supports them but no data on this machine)
- Conversation bodies are read only for keyword / collaboration-style analysis — raw text never appears in the report. Antigravity reads only metadata summaries and markdown headings — no screenshots, no browser cache, no pbtxt annotations.
- Work project names anonymized to "Project A/B/..."; public GitHub repos retain original names (public data is indexable anyway); API keys / tokens / emails scrubbed by regex.
- Report auto-generated by Claude Code / Codex / Cursor / Antigravity local data via Readme.skill v2.5.1; fully reproducible.
- Generated at: **2026-05-28T(local)**
