---
title: Microsoft Copilot Studio Agent 构建、测试与治理
source_id: microsoft-copilot-studio-agent-governance-2026
organization: Microsoft
source_type: official-docs
published: 2026-07-02
verified: 2026-08-08
availability: mixed
confidence: high
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - agent-builder
  - governance
  - evaluation
company_topics:
  - Microsoft Copilot Studio
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Microsoft Copilot Studio Agent 构建、测试与治理

## 一手来源

- [Agent Builder 与 Copilot Studio 对比](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-studio-experience)，更新 2026-07-02；访问 2026-08-08。
- [Copilot Agent Kit](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/kit-overview)，更新 2026-07-16；访问 2026-08-08。
- [Generative orchestration](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/generative-orchestration)，更新 2026-01-20；访问 2026-08-08。
- [Copilot Studio security and governance](https://learn.microsoft.com/en-gb/microsoft-copilot-studio/security-and-governance)，页面未标日期；访问 2026-08-08。
- [Entra Agent IDs](https://learn.microsoft.com/en-us/microsoft-copilot-studio/admin-use-entra-agent-identities)，更新 2026-07；访问 2026-08-08。

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 入口 | Standalone Portal 面向 Maker/Developer 创建 Agent，并可发布到 Teams、Website 或自定义 Endpoint |
| 配置 | Agent、Topic、Action、Flow、Knowledge、Prebuilt/Custom Connector、Instructions |
| 编排 | Generative Orchestration 可规划并选择 Topic、Action 与 Knowledge；支持多步骤逻辑、Branch 和 Approval |
| 目录 | Agent Library 提供 Agent Template 与可复用组件；Agent Inventory 提供 Tenant-wide 清单 |
| 测试 | Agent Kit 支持 Test/Test Set、批量测试、Latency、Observed Response 与 Pass/Fail 结果，以及 Rubric |
| 生命周期 | 支持 ALM、Version 和 Dev/Test/Prod Environment |
| 治理 | Admin 管理 Connector、DLP、Environment、Sharing、Publishing、RBAC、Audit 和 Usage Analytics |
| 身份 | 2026 年能力可为 Agent 创建 Entra Agent ID，显示 Connector Permission 并接入 Conditional Access；相关状态逐项核验 |

## 状态与边界

- 所列页面未给 Copilot Studio 整体统一生命周期；2026 新能力包含 Preview 项，必须逐项标注。
- Copilot Agent Kit 是官方工具包，不代表其中每一组件都是 Copilot Studio 核心服务的默认功能。
- Test Set 与 Rubric 结果不是 CI/CD Test、Policy、Signature 或 Release Approval。
- Copilot Studio 是 Agent 构建与治理平台，官方资料没有把它描述为 WorkBuddy 式专家团或原生软件发布控制面。

## 专题入口

[[50_deepdives/agent-workbench/30_case-map|Agent 构建治理对照矩阵]]
