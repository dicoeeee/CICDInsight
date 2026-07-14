---
title: OpenID AuthZEN Agent 授权工作组草案
source_id: openid-authzen-aarp-coaz-2026-06-15
organization: OpenID Foundation
source_type: standards-working-group-draft
published: 2026-06-15
verified: 2026-07-14
availability: draft
confidence: high
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - authorization
  - approval
  - mcp-policy
company_topics: []
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# OpenID AuthZEN Agent 授权工作组草案

## 来源

- 标题：OpenID Foundation advances authorization for the agent era with new AuthZEN Working Group Drafts
- 发布日期：2026-06-15
- 链接：[OpenID Foundation 官方公告](https://openid.net/openid-foundation-advances-authorization-for-the-agent-era-with-new-authzen-working-group-drafts/)
- 来源类型：标准组织公告与 Working Group Draft
- 能力状态：AARP、COAZ 为正式工作组草案，不是最终标准

## 一句话结论

Agent 遇到“暂时不能执行”时，未来标准方向不是让模型绕过拒绝，而是收集批准、委托和风险证明后重新请求外部策略决策。

## 可核验事实

- AARP 定义批准、同意、委托权、证明、风险评估与说明等授权前置条件的请求、跟踪、满足和重新评估。
- COAZ 把不同来源信息映射为 Subject-Action-Resource-Context，用于 Gateway、Service Mesh 和下游系统授权。
- 首个目标之一是让 MCP Tool 暴露调用前需要的授权检查。
- 官方明确 Policy 仍是最终决策者，批准只是重新决策的输入。

## CI/CD 相关性

- 涉及阶段：全部八阶段，尤其部署、发布和生产写操作。
- 工具类别：外部授权、审批编排、MCP Gateway。
- 自主等级：L2—L4 的控制边界。
- 涉及角色：IAM、安全架构、发布管理、审计。

## 对洞察的价值

为“审批绑定具体计划并在执行时重新校验”提供标准化方向，可避免把权限逻辑写进 Prompt 或 Agent 内部状态。

## 限制与待验证项

- 仍是 Working Group Draft，不代表实现互通或生产采用。
- 不能把它表述为已发布标准或认证要求。

## 可引用判断

- Agent 授权的正确抽象是外部 Policy + 可满足的前置条件 + 执行时重评估，而不是一次性永久授权。
