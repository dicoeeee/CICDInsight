---
title: Octopus Claude Agent Step 功能与 Alpha 边界
source_id: octopus-agentic-deployment-2026
organization: Octopus Deploy
source_type: official-docs
published: 2026-07-07
verified: 2026-08-08
availability: alpha
confidence: high
geography:
  - global
lifecycle_stages:
  - deployment
  - release
  - operations
tool_categories:
  - deployment
  - runbook
  - agent-runtime-orchestration
company_topics:
  - Octopus Deploy
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Octopus Claude Agent Step 功能与 Alpha 边界

## 一手来源

- [Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)，更新 2026-07-07；访问 2026-08-08；Alpha。
- [Claude Agent Step Tools](https://octopus.com/docs/octopus-ai/claude-agent-step/tools)，页面未标日期；访问 2026-08-08。

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 入口 | Deployment Process 或 Runbook 中的 Claude Agent Step |
| 执行位置 | Worker 或 Deployment Target 上运行 Claude Code |
| 上下文 | Project、Environment、Release、Variable、前序 Step Log 和 Output |
| 模式 | 只读调查或在 Target 上执行命令；可启用 Sandbox |
| 工具 | 内置 Octopus MCP、额外 MCP Server、Skills、文件、命令和 Web Tool |
| 权限 | 未授权 Tool Call 被拒绝并导致 Step 失败；无中途交互批准 |
| 记录 | Task Log、Token、Cost 和完整 Transcript |
| 失败语义 | 非零退出、非成功终态、Tool 拒绝或显式 Fail Skill 才失败；Agent 正常退出可能仍显示成功 |

## 状态与接受边界

- 产品为 Alpha，官方明确不建议用于关键、无人值守自动化。
- Agent Step 进入 Deployment/Runbook 不表示可以绕过 Project、Environment 或 Target 权限。
- Task Log 和 Transcript 记录过程，不判断任务目标是否真正完成。
- 重要结果需要后续确定性检查；当前没有中途批准机制。

## 专题入口

[[50_deepdives/agent-workbench/30_case-map|CI/CD 与部署原生 Agent 对照矩阵]]
