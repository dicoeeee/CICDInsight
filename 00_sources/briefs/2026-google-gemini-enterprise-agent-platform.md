---
title: Google Gemini Enterprise Agent Platform 功能与状态
source_id: google-gemini-enterprise-agent-platform-2026
organization: Google Cloud
source_type: official-docs-and-release-notes
published: 2026-06-18
verified: 2026-08-08
availability: mixed
confidence: high
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - agent-runtime-orchestration
  - governance
  - evaluation
company_topics:
  - Gemini Enterprise Agent Platform
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Google Gemini Enterprise Agent Platform 功能与状态

## 一手来源

- [Agents overview](https://docs.cloud.google.com/gemini-enterprise-agent-platform/agents)，更新 2026-07-21；访问 2026-08-08。
- [Agent Development Kit](https://docs.cloud.google.com/gemini-enterprise-agent-platform/build/adk?hl=en)，更新 2026-07-21；访问 2026-08-08。
- [Release notes](https://docs.cloud.google.com/gemini-enterprise-agent-platform/release-notes?hl=en)，页面未标日期；访问 2026-08-08。
- [Vertex AI Agent Engine overview](https://cloud.google.com/vertex-ai/generative-ai/docs/reasoning-engine/overview)，页面未标日期；访问 2026-08-08。

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 管理对象 | Managed Agents API 管理 Agent 配置，并可挂载 Skill/Artifact Sandbox；Interactions API 调用已部署 Agent |
| 构建 | ADK 支持 Agent、Tool、Delegation、Multi-agent Orchestration 和环境模拟 |
| 运行 | Managed Runtime/Agent Engine 部署和扩展 Agent，并提供 Session/Memory 等服务 |
| 工具 | Tool Registry、Agent Gateway、MCP 和 A2A 等用于接入工具或 Agent |
| 身份 | 平台提供 Agent Identity 与受管访问控制功能，具体状态按发布说明核验 |
| 观测 | Agent Observability 提供 Agent/MCP Server 的性能、行为、健康与 Trace 可见性 |
| 评测 | 平台包含 Agent Evaluation/Optimization；部分能力状态为 Preview |
| 状态 | Release Notes 记录 Agent Gateway、Agent Observability、Agent Registry 于 2026-06-18 GA |

## 状态与边界

- 2026-06-18 的三个 GA 项不能外推到平台全部构建、运行、评测和优化能力。
- ADK 的 Multi-agent Orchestration 不等于项目发布审批或 CI/CD Gate。
- Agent Evaluation 提供质量信号，不证明业务结果或生产变更正确。
- 官方资料没有直接证明平台原生执行 GitHub/GitLab Required Check、发布签名或生产批准。

## 专题入口

[[50_deepdives/agent-workbench/30_case-map|Agent 构建治理对照矩阵]]
