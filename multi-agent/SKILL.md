---
name: multi-agent
description: 多代理并行编排。复杂任务先分解、再按角色并行派工，用分支/worktree 隔离避免冲突。当任务≥3步且可并行、多文件/多模块改动、需角色分工（探索/架构/执行/测试/文档）时触发；或用户说「并行做」「分头干」「派多个 agent」。
version: 1.0.0
display_name: "多代理编排"
visibility: "public"
---

# 多代理编排（G4）

把"复杂任务拆给多个分身并行干"固化成流程。借鉴 Bernstein（确定性调度+隔离+预合并验证）、Oh My Claude Code（角色化）思路，适配 WorkBuddy 的 Agent / Task 工具。

## 何时用
- 任务 ≥3 步且彼此独立、可并行
- 多文件 / 多模块改动（易冲突）
- 需要角色分工：探索、架构、执行、测试、文档

## 角色模板（按需取用）
| 角色 | 工具 | 干啥 |
|------|------|------|
| 探索 Explore | Agent(Explore) | 快速找文件/搜代码，不写 |
| 架构 Plan | Agent(Plan) | 设计结构、出方案，不写实现 |
| 执行 general | Agent(general-purpose) | 真正写代码/改文件 |
| 测试 verify | verify skill | 改完跑测试/lint 闭环 |
| 文档 course | codebase-course | 生成可读讲解 |

## 步骤
1. **分解**：用 TaskCreate 把任务拆成小项，标注依赖（`addBlockedBy` / `addBlocks`）。
2. **派工**：相互独立的子任务，在**同一条消息里发多个 Agent 调用**并行跑（true 并行）。
3. **隔离**：多代理都会改代码时，各走独立分支 / worktree，避免互相覆盖同一文件。
4. **汇总**：收齐结果后，用 verify 跑通整体验证，再 present_files。

## 边界（不可越）
- 有依赖链的任务必须串行（B 等 A 的结果），勿假并行。
- 不并行改同一文件——会冲突丢代码。
- 不确定拆法时，先 Plan 角色出方案，再执行。
- 子代理写代码后，主代理必须 verify 复核，不盲信其总结。

## 关联
- TaskCreate/TaskUpdate：任务分解与状态。
- verify：并行结果汇总前必跑。
- guardrail / vcs：派工产出的代码仍受安全与版本纪律约束。
