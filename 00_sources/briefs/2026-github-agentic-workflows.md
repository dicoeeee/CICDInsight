---
title: GitHub Agentic Workflows 功能、编译与 Safe Output 边界
source_id: github-agentic-workflows-2026
organization: GitHub
source_type: official-docs-and-project-docs
published: 2026-02-13
verified: 2026-08-08
availability: public-preview
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
tool_categories:
  - source-control
  - ci-pipeline
  - agent-runtime-orchestration
  - governance
company_topics:
  - GitHub
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitHub Agentic Workflows 功能、编译与 Safe Output 边界

## 一手来源

| 页面 | 状态/核验 | 直接支持的功能 |
|---|---|---|
| [About Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows) | 页面未标日期；访问 2026-08-08；Public Preview | 产品状态、Markdown、Trigger、Engine、Safe Output 与 Actions 执行 |
| [gh-aw project docs](https://github.github.com/gh-aw/) | 页面未标日期；访问 2026-08-08 | Compile、Sandbox、Network、Engine 与 CLI |
| [Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 页面未标日期；访问 2026-08-08 | 只读 Agent Job、隔离写 Job、Secret 与 Threat Detection |
| [Safe Outputs](https://github.github.com/gh-aw/reference/safe-outputs/) | 页面未标日期；访问 2026-08-08 | Issue、Comment、PR 等声明式写出口 |
| [Permissions](https://github.github.com/gh-aw/reference/permissions/) | 页面未标日期；访问 2026-08-08 | Token/API Scope |
| [Ruleset rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) | 页面未标日期；访问 2026-08-08 | PR、Review、Required Check 与 Deployment Gate |

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 状态 | 当前 Public Preview；2026-02-13 公告曾标 Technical Preview |
| Source | `.md` 由 YAML Frontmatter 与 Markdown Instructions 组成 |
| Compile | `gh aw compile` 生成 Hardened `.lock.yml` GitHub Actions Workflow |
| 触发 | Repository Event、Schedule、Web UI 或 `gh aw` CLI |
| 配置 | Trigger、Permissions、Network、Tools、Safe Outputs、Engine |
| Engine | 可配置 Copilot、Claude、Codex、Gemini 等；非默认 Engine 可能需要 Secret |
| 执行 | Agent Job 默认只读，在 Container/Sandbox 和 Network Policy 下运行 |
| 产物 | Safe Output Proposal 由隔离下游 Job 验证并创建 Issue、Comment、PR 等 |
| Secret | 高权限 Secret 保留在 Agent Runtime 外的受控 Job |
| 外部 Gate | Ruleset 可独立要求 PR、Review、Required Check、Deployment Success 与指定 Check 来源 |

## 状态与接受边界

- Source Markdown 与 Compiled `.lock.yml` 都需要审查并提交默认分支。
- Safe Output 是写入控制，不证明输出正确。
- Agent Job 或 Safe Output 不自动成为 Required Check，也不替代 Ruleset、Review 或 Environment Protection。
- PR 创建后的下游 CI 触发取决于 Token、Event 和 Repository 配置。

## 专题入口

[[50_deepdives/agent-workbench/45_github-agentic-workflows|GitHub Agentic Workflows 功能详章]]
