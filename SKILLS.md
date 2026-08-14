# DS 的 Skills 清单（bailacking-skills）

> 这是 DS（你的本地 AI 助手）自我补全的能力包集合，全部为 **自建、适配 WorkBuddy 格式**，非直接复制 GitHub 项目。
> 每个 skill = 一个文件夹，核心是 `SKILL.md`（写明何时用、怎么做）。WorkBuddy 会自动发现并加载。
> 配套讲解课：`DS-skills-course.html`（用 `codebase-course` skill 生成，给小白看）。

## 总览

| # | Skill 名 | 缺口 | 一句话功能 | 触发场景 |
|---|----------|------|-----------|----------|
| 1 | `verify` | G3 | 代码改完必须跑测试/lint/类型检查全绿才算完成 | 改完代码、说「验证」「检查」 |
| 2 | `reuse-eval` | G5 | 写功能前先搜 GitHub 开源评估复用，下载要你确认 | 说「用开源」「找现成」「复用」 |
| 3 | `codebase-course` | G7+G8 | 把代码库转成小白能懂的 HTML 课 | 说「讲讲项目」「看不懂代码」 |
| 4 | `multi-agent` | G4 | 复杂任务分解后按角色并行派工，分支隔离 | 说「并行做」「派多个 agent」 |
| 5 | `guardrail` | G6 | 提交/引依赖前扫密钥+PII+危险依赖 | 提交/推送/加依赖前强制 |
| 6 | `vcs` | G9 | 原子提交+特性分支+PR，破坏性 git 操作先确认 | 说「提交」「推上去」「开 PR」 |
| 7 | `skill-forge` | forge | 把 GitHub 上的 skill 改写适配成 WorkBuddy 能用的，草稿交你确认 | 说「找个 skill」「借鉴/改这个 skill」 |
| 8 | `design-taste-frontend` | taste | 写网页/组件自动带设计意图，破除 AI 模板感 | 说「美化」「别这么 AI 味」「提升审美」 |

## 逐个详解

### 1. verify · 验证闭环（G3）
- **功能**：任何代码/文件改动后，自动跑通 `测试 → lint → 类型检查 →（构建）`，全部通过且有客观证据才算「完成」；失败就 读错→定位→修复→重跑 闭环，严禁「编译过就标记完成」。
- **来源/借鉴**：GitHub 上 `verify-app` 模式（脚本 + 明确 pass criteria）、`ksaday/testing-patterns` 思路。自行改写为 WorkBuddy 可跑的纯指令版（无外部脚本依赖）。

### 2. reuse-eval · 开源复用评估（G5）
- **功能**：遇需求优先搜 GitHub 开源并评估复用，而非重复造轮子。流程：`检索 → 评估候选（许可证红线 GPL/AGPL 传染性筛除、活跃度、适配成本、功能匹配）→ 出对比表+方案 → 等主人「确认执行」才下载整合`。
- **来源/借鉴**：规范参考 `shalomb/agent-skills` 的 SKILL.md 写法；「许可证红线」来自主人 2026-08-14 确立的全局复用纪律。**无现成可复制 skill，为自建**。

### 3. codebase-course · 代码可视化课程（G7+G8）
- **功能**：把代码库转成编程小白能读懂的交互式 HTML 课程（目录树白话、模块讲解、流程图、术语表），专治「看不懂自己代码」。
- **来源/借鉴**：思路来自开源 "Codebase to Course"（把代码变课程，Zara Zhang 等），为中文小白 + WorkBuddy 重新设计流程与输出样式（深色自适应单页 HTML）。

### 4. multi-agent · 多代理编排（G4）
- **功能**：复杂任务先 `TaskCreate` 分解，再按角色（探索/架构/执行/测试/文档）并行派工；共享改动走分支/worktree 隔离，不并行改同一文件。
- **来源/借鉴**：`Bernstein`（确定性调度 + worktree 隔离 + 预合并验证）、`Oh My Claude Code`（32 角色 + 5 模式）、`gstack` / `h5i` / `SwarmCode` 的并行编排思路。适配为 WorkBuddy 的 Agent/Task 工具调用。

### 5. guardrail · 安全护栏（G6）
- **功能**：提交/推送/引依赖前强制扫描密钥（api_key/token/.env/私钥）、PII（身份证/手机/邮箱）、危险依赖（拼写仿冒、未知来源、GPL/AGPL 红线）；命中即阻断，绝不硬编码密钥。
- **来源/借鉴**：`bassalat/audit-sensitive`（推送前扫密钥/PII）、`h5i` auto-audit、`Semgrep`、`Bandit` 思路。简化为 WorkBuddy 可跑的 grep 清单 + 红线规则。

### 6. vcs · 版本纪律（G9）
- **功能**：改动须原子提交、带描述、可回滚；特性分支开发、走 PR；破坏性 git 操作（reset --hard / push --force）先给方案等确认；不开 `--no-verify` 绕过钩子。
- **来源/借鉴**：`ksaday/github-workflow`、`wshobson/commands`、`aider`（git-aware 自动提交）的版本工作流思路。

### 7. skill-forge · Skill 锻造（forge · 额外补全）
- **功能**：GitHub 上的 skill（Claude Code / Cursor 生态）直接用在 WorkBuddy 会环境/语法不兼容。本 skill 是「专项翻译机」：**只读 fetch 外部 skill → 映射成 WorkBuddy 的 SKILL.md 格式 → 标出不兼容点 → 在 `test` 目录产可加载草稿 → 交大帅「确认执行」才安装**。绝不照搬、绝不自动安装。
- **来源/借鉴**：无现成可复制 skill，为自建。核心资产是「Claude/Cursor → WorkBuddy 格式映射表」（见 SKILL.md 正文），把 `$ARGUMENTS` / `allowed-tools` / `hooks` / `@file` 等外部语法逐一改写为 WorkBuddy 认的格式。
- **与 reuse-eval 的关系**：reuse-eval 是「要不要复用」的总闸门；skill-forge 是「怎么改成能跑的」专项。顺序：reuse-eval 评估通过 → skill-forge 改写 → 大帅确认执行。

### 8. design-taste-frontend · 前端审美提升（taste · skill-forge 实装样例）
- **功能**：写/改网页、落地页、组件、UI 时，自动带「设计意图」——三拨盘（变化度/动效/密度）、AI Slop 禁令清单（杜绝紫渐变+三卡片+居中模板感、em-dash 零容忍）、输出前预检清单、改旧项目的重设计协议。告别「能跑但像 AI 批量生成」的丑界面。
- **来源/借鉴**：GitHub `Leonxlnx/taste-skill`（**MIT**，v2 纯指令文档）。经 `reuse-eval` 过红线（MIT 安全）+ `skill-forge` 改写适配（仅补 WorkBuddy frontmatter、译中文、删 Claude/Cursor 安装路径），是本仓库**第一个用 skill-forge 流程实装进来的外部 skill**。

## 使用约定（重要）
- **这些都是「能力包」，不是程序**：不独立运行，DS 在对话中遇到对应场景自动加载照做。
- **GitHub 上的 skill 不能直接用**：Claude Code/Cursor 生态的 skill 在 WorkBuddy 有环境/语法差异（工具名、`$ARGUMENTS`、hooks 不同）。本仓库全部为**借鉴思路后自行改写适配**的可用版本。
- **下载整合开源仍受限**：任何要拉 GitHub 源码进项目的行为，仍受 `reuse-eval` + `guardrail` 约束，需主人「确认执行」。

## 仓库结构
```
bailacking-skills/
├── SKILLS.md              ← 你正在看的这份索引
├── DS-skills-course.html  ← 讲解课（小白向）
├── verify/SKILL.md
├── reuse-eval/SKILL.md
├── codebase-course/SKILL.md
├── multi-agent/SKILL.md
├── guardrail/SKILL.md
├── vcs/SKILL.md
├── skill-forge/SKILL.md
└── design-taste-frontend/SKILL.md
```
