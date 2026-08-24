---
title: Harness Inc. Worker Agents 功能与 Pipeline 边界
source_id: harness-worker-agents-2026-07-08
organization: Harness Inc.
source_type: official-docs
published: 2026-07-09
verified: 2026-08-09
availability: official-docs-no-unified-feature-stage
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
  - deployment
  - release
tool_categories:
  - ci-pipeline
  - security
  - infrastructure-deployment
  - agent-runtime-orchestration
  - governance
company_topics:
  - Harness
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Harness Inc. Worker Agents 功能与 Pipeline 边界

## 一手来源

| 页面 | 页面日期/核验 | 直接支持的功能 |
|---|---|---|
| [Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/) | 更新 2026-07-22；访问 2026-08-09 | Catalog、Definition、Pipeline Stage、Version、Container Execution |
| [Worker Agent configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) | 更新 2026-07-22；访问 2026-08-09 | Inputs、Agent Settings、Trigger、Output、Reasoning Turns |
| [Example agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/) | 更新 2026-07-23；访问 2026-08-09 | IaC Safety 与 Spec/Plan/Code Pipeline 示例 |
| [Worker Agent security](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness) / [Identity and permissions](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness) | 发布 2026-07-13/16；访问 2026-08-09 | Process/Secret/Network 隔离、Principal/Grant 与 Tool Allowlist |
| [Worker Agents product page](https://www.harness.io/products/platform/worker-agents) / [Launch blog](https://www.harness.io/blog/introducing-autonomous-worker-agents) | 发布 2026-06-30；访问 2026-08-09 | Autofix 读取失败 PR Build、写回 PR Branch、重触发 CI 与 Max Turns |

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 入口 | Account/Organization/Project Agent Catalog 与 Pipeline Worker Agent Step |
| 定义 | Name、Description、Instructions、Model Connector、MCP、Typed Inputs、Environment Variables、Maximum Reasoning Turns |
| 版本 | Pipeline 用 `agentName: name@version` 引用目录定义，并通过 Inputs/Agent Settings 注入参数 |
| 上下文 | Harness Expression、Pipeline Input、前序 Output、Artifact/Manifest、Secret Reference |
| 触发 | Manual、Webhook、Artifact、Manifest、Schedule；具体 Principal Context 依 Trigger 而异 |
| 协作 | 多个 Agent 可按 Pipeline 顺序串接；官方示例展示 Specification、Plan、Code 三段 |
| 产物 | `$HARNESS_OUTPUT`/Output Variable 供后续 Condition、Approval、Notification 或 Step 使用 |
| 示例 | IaC Plan Safety 输出 `APPROVE/REVIEW/REJECT` 候选值；开发者在 Merge 前审查生成物 |
| CI Autofix | Managed Autofix 读取失败 PR Build Log、定位根因、提交到 PR Branch、重触发 CI，直到 Build 通过或达到最大轮次 |
| 权限 | Parent RBAC 与 Declared Grant 取交集；MCP Tool 受 Connector 与 Agent Allowlist 交集 |
| 隔离 | Container/VM、Agent/Broker/Egress 用户、Host-bound Secret Placeholder 与 Network Control |

## 状态与接受边界

- 官方页面没有为所有 Worker Agent 子能力提供统一 GA/Preview/Beta 标签。
- 使用前置条件、Model Connector、Secret 与 Pipeline Permission 依账户配置。
- Event Trigger 不总能继承人类 Principal 的 Scoped Token。
- Managed Worker Autofix 与旧 Code Quality AutoFix Run Step/API 是不同执行面；不能自动共享相同身份、沙箱或权限结论。
- Output Variable 与示例分类值是 Pipeline 数据，不等于 Test、Policy、Approval 或 Deployment 已通过。
- “Build 通过”是厂商文档所述验证终点，不自动等于仓库全部 Required Checks 或业务语义正确。
- 厂商示例只证明配置可表达，不是跨客户效果数据。

## 专题入口

[[50_deepdives/agent-workbench/44_harness-worker-agents|Harness Inc. Worker Agents 功能详章]]
