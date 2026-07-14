---
title: SAMOS MCP Agent 工作流安全研究
source_id: ibm-brown-samos-2025-10-13
organization: IBM Research / Brown University
source_type: workshop-paper
published: 2025-10-13
verified: 2026-07-14
availability: research
confidence: medium
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - mcp-gateway
  - information-flow-control
  - agent-security
company_topics:
  - IBM
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# SAMOS MCP Agent 工作流安全研究

## 来源

- 标题：Securing MCP-based Agent Workflows
- 发布日期：2025-10-13；SOSP/PACMI 2025
- 链接：[IBM Research](https://research.ibm.com/publications/securing-mcp-based-agent-workflows)
- 来源类型：ACM Workshop Paper
- 能力状态：研究原型

## 一句话结论

MCP 工具调用的安全策略应在 Agent 外部网关确定性执行，不能只靠 Prompt 自律。

## 可核验事实

- SAMOS 在 MCP Gateway 拦截工具调用，并根据开发者或管理员声明的策略执行信息流控制。
- 论文使用 GitHub MCP Server 的漏洞案例验证阻断攻击同时保留原功能。

## CI/CD 相关性

- 涉及阶段：全部八阶段的 Agent 工具调用。
- 工具类别：MCP Gateway、信息流控制、安全策略。
- 自主等级：L2—L4。
- 涉及角色：平台安全、Agent 平台和工具服务所有者。

## 对洞察的价值

为“模型负责推理、外部控制面负责边界”的架构原则提供研究依据。

## 限制与待验证项

- Workshop Paper 验证规模较小，主要基于单一安全案例。

## 可引用判断

- 高权限 Agent 的安全边界需要由模型外部的确定性控制面执行。
