---
name: skill-forge
description: 获取/锻造 Skill。当用户想从 GitHub（Claude Code / Cursor 等生态）引入一个 skill，或说「找个 skill」「借鉴这个 skill」「把这个 skill 改成 WorkBuddy 能用的」「下载个 skill」，或我准备下载整合外部 skill 时触发。负责：只读 fetch → 映射成 WorkBuddy 的 SKILL.md 格式 → 标出不兼容点 → 在 test 目录产出可加载草稿 → 交大帅确认执行。绝不自动安装、绝不照搬外部文件。
version: 1.0.0
display_name: "Skill 锻造"
visibility: "user"
---

# Skill Forge — 外部 Skill 的获取与改写

## 定位
GitHub 上的 skill 大多是 **Claude Code / Cursor 生态**，直接用在 WorkBuddy 会有环境/语法差异（工具名、`$ARGUMENTS`、hooks、`allowed-tools` 都不认）。
本 skill 是「**专项翻译机**」：把外部 skill 改写成真正能在 WorkBuddy 跑起来的版本。

它是 `reuse-eval`（总闸门：要不要复用 + 许可证红线）的**互补专项**，不是替代：
- `reuse-eval` 决定「要不要复用这个外部东西」。
- `skill-forge` 决定「怎么把它改成 WorkBuddy 能跑的」。

## 何时用
- 大帅说「从 GitHub 找个 skill」「借鉴/改写这个 skill」「把这个 skill 弄成我能用的」「下载个 skill」。
- 我准备下载整合外部 skill 前，先走本 skill。

## 硬边界（不可动摇）
1. **只读获取**：可用 WebFetch / `git clone`（分析动作，属检索范畴）读取外部 skill 源码，但不改、不装。
2. **不自动安装**：产出的 SKILL.md 只是**草稿**，写进 `~/.workbuddy/skills/` 与 `D:\workspace\skills/` 必须等大帅「确认执行」。
3. **不突破 reuse-eval 闸门**：若外部 skill 是 GPL/AGPL 等传染性协议且涉及闭源商用，先按 reuse-eval 排除并提示。
4. **草稿落 test**：改写产物先放 `D:\workspace\test\`（遵守文件创建纪律），不直接进 skills 目录。

## 执行流程（逐步）

### Step 1 · 锁定来源
- 记录外部 skill 的 URL / 仓库 / 文件树。
- 用 WebFetch 读 README 与入口文件：
  - Claude Code：通常 `SKILL.md`，可能带 `scripts/`、`references/`、`assets/`。
  - Cursor：通常 `.mdc` 文件，内含 `@file` / `@github` / `@web` 引用与 `<mcp_tool>` 块。
  - 其他：GitHub 上也叫 `SKILL.md` 或 `*.md`。

### Step 2 · 格式映射（核心：Claude/Cursor → WorkBuddy）
逐条对照下表改写，**原样留下的会静默失效，必须改**：

| 外部写法 | WorkBuddy 对应处理 | 说明 |
|----------|-------------------|------|
| `name` / `description` / `version` | 同字段保留 | 字段名一致，直接用 |
| `description` 触发词 | 写进 description 的自然语言触发词 | WorkBuddy 靠 description 匹配触发 |
| `allowed-tools: [...]` | **删除**，改为正文「调用工具」段落说明 | WorkBuddy 无此 frontmatter 键 |
| `$ARGUMENTS` / 斜杠命令参数 | 改为正文「从用户消息提取参数」说明 | 无变量替换语法 |
| `hooks` / 自动触发脚本 | **删除**，改为 description 描述触发场景 | 触发器只能在 description 表达 |
| `@file` / `@github` / `@web` 引用 | 改为绝对路径或文字说明 | WorkBuddy 无该引用语法 |
| `<mcp_tool>` / `<command>` 块 | 改为普通文字指令（调哪个工具、做什么） | 去掉 XML 标签，保留语义 |
| `model: ` 指定模型 | **删除**（由 WorkBuddy 调度） | |
| Bash/Python 脚本 | 放进 skill 文件夹内 `scripts/` 子目录，相对路径调用 | 保持可移植 |
| `reference:` / `assets:` 引用 | 保留文件，用相对路径引用 | 同目录结构即可 |

### Step 3 · 标出不兼容点
- 在草稿**顶部**用 `> ⚠️ 改写说明` 区块，逐条列出：原版有什么、WorkBuddy 不支持什么、我怎么处理的。
- 评估改写成本（低 / 中 / 高），给大帅预期。
- 若原版强依赖某个 WorkBuddy 没有的能力（如特定 MCP、专用 CLI），明确写「此 skill 无法完整迁移，只能做降级版」。

### Step 4 · 产出草稿
- 在 `D:\workspace\test\` 生成改写后的 `SKILL.md`（草稿，不进正式 skills 目录）。
- 含：
  - frontmatter：`name` / `description`（含触发词）/ `version` / `display_name` / `visibility: "user"`
  - 正文：定位 / 何时用 / 硬边界 / 执行流程 / 工具调用说明 / 改写说明 / 关联
  - 若有脚本，一并放 `test/<name>/scripts/`

### Step 5 · 交确认
- 把草稿路径 + 改写说明 + 不兼容点摘要发给大帅，等「确认执行」。
- 大帅确认后，才复制到 `~/.workbuddy/skills/<name>/` 与 `D:\workspace\skills/<name>/`，并更新 `D:\workspace\skills\SKILLS.md` 索引。

## 与 reuse-eval 的分工
1. **先** `reuse-eval` 评估通过（要不要复用、许可证红线、活跃度、适配成本、候选对比）。
2. **再** `skill-forge` 改写（格式映射、不兼容标注、草稿生成）。
3. **最后** 大帅「确认执行」→ 落地安装 + 更新索引。

## 反模式（禁止）
- 直接 `cp` 外部 SKILL.md 进 skills 目录（环境不兼容，必炸）。
- 把 `$ARGUMENTS` / `allowed-tools` / `hooks` 原样留下（WorkBuddy 不认，静默失效）。
- 未经确认就安装（突破铁律）。
- 吞掉不兼容点不告知（违反诚实无知纪律）。

## 关联
- `reuse-eval`：总闸门，改造前先过它。
- `guardrail`：若草稿里要引依赖/写密钥，先过它。
- `verify`：改写后的 skill 若含代码，落地后验证。
- `codebase-course`：落地后可生成讲解课。
