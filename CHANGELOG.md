# Changelog

本项目遵循 [Semantic Versioning](https://semver.org/lang/zh-CN/) 与 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 风格。

## [Unreleased]

## [2.5.0] - 2026-05-28

> **「四引擎」时代** — 从 Claude+Codex 双引擎扩展到 4 个 AI 编程工具。

### Added
- **Kiro (AWS) 完整支持** — SKILL.md `Step 3b`：`~/.kiro/sessions/cli/*.{json,jsonl}` + `~/.local/share/kiro-cli/data.sqlite3` + `~/.kiro/{agents,skills,steering,prompts,settings}`。
- **Trae (ByteDance) 部分支持** — SKILL.md `Step 3c`：`~/Library/Application Support/Trae/User/{workspaceStorage,globalStorage}/state.vscdb`（chat 元数据）+ 项目 `.trae/{rules,skills}`。**Trae token 用量走云端 API，本 skill 保持离线，token 数字默认缺失**。
- **Gemini Antigravity (Google) 支持** — SKILL.md `Step 3d`，来自社区 PR [#1](https://github.com/study8677/Readme.skill/pull/1) by [@BaekElk19](https://github.com/BaekElk19)：`~/.gemini/antigravity/brain/<uuid>/` 每任务 metadata + `task.md` / `implementation_plan.md` / `walkthrough.md`。token 不可得时按 task/artifact 计数 + text-scale 估算（明确标注 non-billing）。
- **新触发词**：「分析我的 Kiro / Trae / Antigravity 使用情况」/ "summarize my Kiro / Trae / Antigravity history"。
- **多工具编排升级**：Step 6.4 综合分数与编排模式从「双引擎」扩展为「多引擎」，支持 Claude + Codex + Kiro + Trae + Antigravity 五选 N。
- **降级策略表**新增 Kiro / Trae / Antigravity 缺失时的处理。
- **GitHub Discussions** 开启，新增 `Show your profile` 引导。
- **`.github/ISSUE_TEMPLATE`** YAML form：bug / feature / new-tool-support。
- **CHANGELOG.md**（本文件）。
- **README badges + Roadmap + FAQ + 对比表 + Community CTA**。

### Changed
- README 整段重写，按热门项目模版（用户语言开场 + 受众矩阵 + 30 秒装上 + 对比同类）。
- GitHub Topics 6 → 20，新增 `kiro` `trae` `claude-skill` `readme-generator` 等。
- plugin / marketplace.json description 反映 4 工具支持。

### Resolved Issues
- [#2](https://github.com/study8677/Readme.skill/issues/2) Add Kiro + Trae.ai support。

## [2.4.0] - 2026-05-08

> **链式传播海报** — 海报自带「金句 + 徽章 + 安装 CTA」三件套，看到的人能立刻生成自己的。

### Added
- **SVG 海报渲染** (`Step 8b`) — 1080×1920 竖屏，6 个英雄数字 + Evolution timeline。
- **AI 自评金句**（Tone A-F）：把 token 量换算成「等于 N 万遍《红楼梦》」破圈类比。
- **身份徽章自动判定**：TWO-ENGINE / CACHE MASTER / SKILL BUILDER / POLYGLOT / TOKEN WHALE 等。
- **30 秒安装 CTA**：海报底部直接放 install 命令 + 仓库 URL。
- **海报双语**：自动按提问语言出中/英版，技术术语保留英文。

### Featured In
- 🎉 [阮一峰《科技爱好者周刊》第 395 期](https://www.ruanyifeng.com/blog/2026/05/weekly-issue-395.html) — 2026-05-08。

## [2.3.0]

### Fixed
- Skill description 多行 YAML（`>` folded / `|` literal）解析 bug。之前用 `head | grep` 会静默截断到只剩 `>`。

## [2.2.0]

### Added
- **💎 Token 经济学** 作为第 10 个维度：cache leverage / 模型迁移 / 月度趋势 / 模型迁移注解。

## [2.1.0]

### Added
- **作为 Claude Code Plugin 发布**（两行命令安装）。
- 允许读取 `message.content` 用于关键词与协作风格深度分析（不写进报告原文）。

## [2.0.0]

> **从「炫数据」到「讲故事」** —— 报告重心从堆 token 量改为讲 AI-Native 成长弧线。

### Added
- **🚀 Velocity & Leverage** —— AI 让你快了多少、广了多少。
- **🔧 AI 基础设施** —— 你给 AI 造了什么工具。
- **🧬 Evolution 曲线** —— AI 用法的成长弧线时间线。
- **Session 架构分析**：plan-first 占比、context 管理、effort 切换。
- **双工具编排模式**：每个项目标注 Claude 主导 / Codex 主导 / 双引擎。

### Changed
- GitHub 贡献优先，token 表降级到「参考」。

## [1.0.0]

### Added
- 初始版本：Readme.skill 作为 Claude Code / Codex CLI 的 skill，从本地数据生成 AI-Native 开发者画像。
- 10 个维度的 Markdown profile。
- 默认匿名 + OWASP-style 正则清洗。

[Unreleased]: https://github.com/study8677/Readme.skill/compare/v2.5.0...HEAD
[2.5.0]: https://github.com/study8677/Readme.skill/compare/v2.4.0...v2.5.0
[2.4.0]: https://github.com/study8677/Readme.skill/compare/v2.3.0...v2.4.0
[2.3.0]: https://github.com/study8677/Readme.skill/compare/v2.2.0...v2.3.0
[2.2.0]: https://github.com/study8677/Readme.skill/compare/v2.1.0...v2.2.0
[2.1.0]: https://github.com/study8677/Readme.skill/compare/v2.0.0...v2.1.0
[2.0.0]: https://github.com/study8677/Readme.skill/compare/v1.0.0...v2.0.0
[1.0.0]: https://github.com/study8677/Readme.skill/releases/tag/v1.0.0
