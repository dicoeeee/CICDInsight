---
title: HolmesGPT SRE Agent
source_id: holmesgpt-2026-05-27
organization: HolmesGPT / CNCF Sandbox
source_type: official-open-source-release
published: 2026-05-27
verified: 2026-07-14
availability: open-source
confidence: high
geography:
  - global
lifecycle_stages:
  - deploy
  - post-release
tool_categories:
  - observability
  - sre-agent
  - mcp
company_topics: []
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# HolmesGPT SRE Agent

## 来源

- 标题：HolmesGPT 0.31.0
- 发布日期：2026-05-27
- 链接：[官方 Release](https://github.com/HolmesGPT/holmesgpt/releases/tag/0.31.0)
- 来源类型：CNCF Sandbox 开源项目发布
- 能力状态：稳定开源版本

## 一句话结论

发布后 Agent 正从单日志问答转向跨 Kubernetes、指标、日志和配置的多工具调查，但闭环修复仍通常需要外部系统和明确授权。

## 可核验事实

- 项目连接 Kubernetes、Prometheus、Grafana、Elasticsearch 等上下文。
- 0.31 增加指标分析评测、动态 Session Token 的 MCP 工具集、周期性工具发现以及多实例、多区域支持。
- 项目文档强调只读与 RBAC。

## CI/CD 相关性

- 涉及阶段：部署验证、发布后观测和事故调查。
- 工具类别：可观测、SRE Agent、MCP。
- 自主等级：L1—L2。
- 涉及角色：SRE、平台工程和事件响应团队。

## 对洞察的价值

它代表高影响力开源项目在发布后诊断方向的能力边界和治理选择。

## 限制与待验证项

- 开源发布证明功能，不证明大型企业生产成效。
- 主要偏分析与调查，不应等同于 L4 自动恢复。

## 可引用判断

- 发布后 Agent 的近期成熟点是跨源调查，自动恢复仍需更强的权限、验证和爆炸半径控制。
