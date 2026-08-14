---
name: guardrail
description: 安全护栏。动手/提交/推送/下载整合开源前，扫描密钥与 PII 泄露、危险依赖，拦在出错前。当用户说「提交」「推送」「加依赖」「整合开源」，或我准备写含凭证的代码、准备 git 操作前触发。
version: 1.0.0
display_name: "安全护栏"
visibility: "public"
---

# 安全护栏（G6）

把"提交前先扫一遍安全隐患"固化为默认关卡。回应 vibe coding 实测头号风险：约 45% AI 生成代码有安全隐患，小白最易踩（硬编码密钥、跳过校验）。借鉴 bassalat audit-sensitive / h5i auto-audit / Semgrep / Bandit 思路，适配为 WorkBuddy 可跑的 grep 清单。

## 何时用（强制）
- 任何 `git commit` / `git push` 之前
- 引入外部依赖（npm/pip 安装）之前
- 写含凭证/配置的代码之前
- `reuse-eval` 决定下载整合开源之前

## 扫描清单
**1. 密钥 / 凭证**（命中即阻断）
- 模式：`api_key` `apikey` `secret` `token` `password` `passwd` `private_key` `access_key` `AKID` `ghp_` `sk-` `AIza` `-----BEGIN ... PRIVATE KEY-----`
- 文件：`.env`（勿提交）、`*.pem` `*.key` `id_rsa`、含明文的 config/credentials
- 规则：**绝不硬编码密钥到仓库**；改用环境变量 / 密钥管理；`.gitignore` 排除 `.env`

**2. PII（个人敏感信息）**
- 身份证号、手机号、邮箱明文、银行卡号——确认非示例/非脱敏再处理

**3. 危险依赖**
- 拼写仿冒（slopsquatting）：包名与你意图差一个字母
- 未知来源、无许可证、长期未维护、下载量异常低
- 许可证红线：GPL / AGPL 传染性协议，闭源商用场景排除（见 reuse-eval）

## 步骤
1. 对暂存/改动文件跑 grep 模式扫描（区分大小写 + 常见前缀）。
2. 命中密钥/PII → **阻断提交**，提示主人改用环境变量或脱敏；`.gitignore` 补 `.env`。
3. 引入依赖 → 核对来源与许可证，排除仿冒与 GPL/AGPL。
4. 清零后再放行 commit / push / 整合。

## 红线（不可越）
- 绝不把真实密钥写进 git 历史；一旦误提交，立即告知主人走「撤销/轮换」流程。
- 不开 `--no-verify` 跳过 pre-commit 钩子来绕过本关卡。

## 关联
- reuse-eval：依赖/开源整合前的许可证与来源评估。
- verify：扫完放行后，改动仍须跑测试/lint。
- vcs：本关卡是提交前的强制门。
