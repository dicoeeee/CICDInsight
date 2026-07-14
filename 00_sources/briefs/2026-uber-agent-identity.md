---
title: Uber 生产级 Agent 身份与委托链
source_id: uber-agent-identity-2026-05-21
organization: Uber
source_type: first-party-production-architecture
published: 2026-05-21
verified: 2026-07-14
availability: internal-production
confidence: high
geography:
  - united-states
  - global-engineering
lifecycle_stages:
  - cross-cutting
tool_categories:
  - identity
  - authorization
  - mcp-gateway
  - audit
company_topics:
  - uber
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Uber 生产级 Agent 身份与委托链

## 来源

- 标题：Solving the Identity Crisis for AI Agents
- 发布日期：2026-05-21
- 链接：[Uber Engineering 官方文章](https://www.uber.com/au/en/blog/solving-the-agent-identity-crisis/)
- 来源类型：大型企业第一方生产架构实践
- 能力状态：身份基础已内部生产使用；动态访问与统一策略部分属于未来方向

## 一句话结论

CI/CD Agent 的身份不能停在共享 Bot Token；企业需要同时证明“哪个 Agent、代表哪个人、经过哪些 Agent、调用了哪个工具”。

## 可核验事实

- Agent Registry 将 Agent 绑定到 SPIFFE/SPIRE 工作负载身份。
- STS 为每一跳生成短时、特定 Audience 的 JWT，并携带完整 actor chain。
- MCP Gateway 根据人类与 Agent 双重身份执行工具级策略、脱敏和审计。
- Uber 披露该系统被数千个内部 Agent 采用，STS Token Exchange P99 持续低于 40 ms。

## CI/CD 相关性

- 涉及阶段：全部八阶段，尤其仓库写入、部署和恢复动作。
- 工具类别：Agent Registry、STS、SPIFFE/SPIRE、MCP Gateway、审计。
- 自主等级：L2—L4 的基础控制。
- 涉及角色：IAM、安全平台、研发效能、SRE、审计。

## 对洞察的价值

把 NIST 的原则性建议补成生产实现：任务级身份、短期凭据、委托链和网关策略可以在大规模内部 Agent 上落地。

## 限制与待验证项

- 采用量与时延为第一方披露。
- 动态风险访问和统一策略执行被写成长期方向，不能当作全部已上线能力。

## 可引用判断

- 企业 Agent 身份的目标不是“给 Bot 一个账号”，而是把人到工具的完整委托链变成可验证、可授权、可审计的运行事实。
