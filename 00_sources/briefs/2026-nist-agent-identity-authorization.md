---
title: NIST 软件与 AI Agent 身份授权概念稿
source_id: nist-nccoe-agent-identity-2026-02-05
organization: NIST NCCoE
source_type: government-concept-paper
published: 2026-02-05
verified: 2026-07-14
availability: research
confidence: high
geography:
  - united-states
  - global-reference
lifecycle_stages:
  - cross-cutting
tool_categories:
  - identity
  - authorization
  - audit
company_topics: []
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# NIST 软件与 AI Agent 身份授权概念稿

## 来源

- 标题：Accelerating the Adoption of Software and Artificial Intelligence Agent Identity and Authorization
- 发布日期：2026-02-05
- 链接：[NIST NCCoE 官方页面](https://csrc.nist.gov/pubs/other/2026/02/05/accelerating-the-adoption-of-software-and-ai-agent/ipd)
- 来源类型：政府概念稿、公开征求意见材料
- 能力状态：Initial Public Draft，不是正式标准

## 一句话结论

Agent 进入代码仓、流水线、制品仓和云环境后，企业必须把其视为独立数字主体治理，而不是沿用共享机器人账号或把授权埋在 Prompt 中。

## 可核验事实

- 文档将 Agent 身份、授权、审计、不可否认性及提示注入控制列为企业采用的关键问题。
- 讨论固定身份与任务级临时身份、身份元数据、委托主体，以及 OAuth/OIDC 等既有机制。
- 文档指出 MCP 使用 OAuth 作为 Agent 访问授权的重要机制。

## CI/CD 相关性

- 涉及阶段：全部八阶段，尤其是制品、部署、发布和恢复。
- 工具类别：身份、授权、审计和 Agent 控制面。
- 自主等级：L2—L4。
- 涉及角色：平台安全、IAM、审计、SRE 和研发效能团队。

## 对洞察的价值

可据此形成落地原则：每个 Agent/任务有独立身份、最小权限、短期凭据、明确委托链和完整审计。

## 限制与待验证项

- 这是概念稿和研究问题清单，未形成经过验证的参考实现或强制合规要求。

## 可引用判断

- NIST 正把 Agent 身份与授权列为企业采用问题；不能将该概念稿表述为已发布标准。
