---
name: vcs
description: 版本控制纪律。改动必须原子提交、带描述、可回滚；特性分支开发、走 PR。当用户说「提交」「保存进度」「推上去」「开 PR」，或我完成一次改动要落盘时触发。
version: 1.0.0
display_name: "版本纪律"
visibility: "public"
---

# 版本纪律（G9）

把"改动怎么进 git"固化为纪律，杜绝一改全崩、无法回滚。借鉴 ksaday github-workflow / wshobson commands / aider git-aware 思路，适配 WorkBuddy 的 /commit 与 git 工具。

## 何时用
- 任何要落盘的改动（写完一个逻辑功能 / 修完一个 bug）
- 准备 commit / push / 开 PR

## 原则
- **原子提交**：一个逻辑改动 = 一次提交；不把"重构+新功能+修bug"混一提交。
- **可验证**：提交前该改动已通过 verify（测试/lint 全绿）。
- **带描述**：message 让人（小白主人）看懂"改了啥、为啥"。
- **特性分支**：开发在分支，不直接冲 main/master；重大改动走 PR。
- **不跳过钩子**：不用 `--no-verify` 绕过 pre-commit（含 guardrail）。

## 提交信息格式（Conventional Commits 简化版）
```
<type>: <简述>
```
- `feat` 新功能 · `fix` 修 bug · `docs` 文档 · `refactor` 重构 · `test` 测试 · `chore` 杂务
- 例：`feat: playerthree 新增头戴鼠标校准配置读取`

## 步骤
1. 切/建特性分支（`feature/xxx` 或 `fix/xxx`）。
2. 小步改动；暂存**相关文件**，不用 `git add -A` 一把全加（避免混进密钥/临时文件）。
3. guardrail 先扫（密钥/PII/危险依赖）—— 未过不许提交。
4. verify 跑通（若项目有测试/lint）。
5. 用 `/commit` 命令提交（自带安全协议，优于裸 `git commit`）。
6. 推分支 → 开 PR 描述（改了啥/怎么测的/风险）。

## 边界（破坏性操作须先确认）
- `git reset --hard` / `git push --force` / `git checkout` 覆盖未提交改动：**先给方案，等主人「确认执行」**。
- 不主动 push 到 main/master；不替主人 merge PR。
- 误提交密钥：立即告知，走「回退 + 轮换密钥」流程，不沉默。

## 关联
- guardrail：提交前强制安全门。
- verify：提交前验证门。
- multi-agent：并行派工产出的代码统一走本纪律提交。
