---
title: Atlassian Rovo Agents、Subagents、Skills 与治理
source_id: atlassian-rovo-platform-driven-development-2026
organization: Atlassian
source_type: official-docs
published: null
verified: 2026-08-08
availability: official-docs-no-unified-stage
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - cross-cutting
tool_categories:
  - agent-runtime-orchestration
  - developer-workflow
  - governance
company_topics:
  - Atlassian Rovo
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Atlassian Rovo Agents、Subagents、Skills 与治理

## 一手来源

- [Rovo Agents](https://support.atlassian.com/rovo/docs/agents/)，页面未标日期；访问 2026-08-08。
- [Create and edit agents](https://support.atlassian.com/rovo/docs/create-and-edit-agents/)，页面未标日期；访问 2026-08-08。
- [Agent permissions and governance](https://support.atlassian.com/rovo/docs/rovo-agent-permissions-and-governance/)，页面未标日期；访问 2026-08-08。
- [Rovo Dev Agent Skills](https://support.atlassian.com/rovo/docs/extend-rovo-dev-cli-with-agent-skills/)，页面未标日期；访问 2026-08-08。

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 入口 | Rovo Chat、Jira/JSM/JPD/Confluence、Automation Rule、Studio 和 Rovo Dev CLI |
| Agent | 可用自然语言或表单配置 Identity、Description、Behavior、Conversation Starter |
| Subagent | 为不同条件配置 Instructions、Knowledge 和 Tools；默认 Subagent 在无其他匹配时使用 |
| Knowledge | 可连接 Confluence、Jira、Google Drive、Deep Research 或 Web Search 等来源 |
| Tools | Agent 可在用户确认和既有权限范围内创建、编辑或组织 Atlassian 对象 |
| Skills | Rovo Dev 使用 `SKILL.md`；支持 Built-in、User、Project Scope 和优先级 |
| 管理 | Owner/Admin 编辑 Agent；Studio Admin 可限制谁能创建 Agent |
| 权限 | Agent 不授予额外数据权限；用户只能看到和操作自己已有权限范围内的数据 |

## 状态与边界

- 所列页面没有统一标注 Rovo Agents/Subagents 的 GA/Beta/Preview 状态。
- Subagent 配置不证明公开了并行调度、隔离或质量基准。
- 用户权限继承不等于全局管理员或生产发布批准。
- Rovo 面向 Atlassian 工作面与开发 CLI；官方资料没有证明它是 CI/CD 原生发布控制面。

## 专题入口

[[50_deepdives/agent-workbench/30_case-map|终端工作台与治理对照矩阵]]
