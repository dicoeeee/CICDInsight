---
title: Harness Worker Agent 隔离、身份与权限
source_id: harness-worker-agent-security-2026-07-16
organization: Harness
source_type: official-engineering
published: 2026-07-16
verified: 2026-07-16
availability: ga-with-flags
confidence: medium-high
geography:
  - global
lifecycle_stages:
  - cross-stage
tool_categories:
  - agent-runtime-orchestration
  - identity-authorization
  - security
  - governance
company_topics:
  - Harness
autonomy_levels:
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Harness Worker Agent 隔离、身份与权限

## 来源

- 标题：[How We Secured AI Worker Agents in Harness](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness)；[Identity and Permissions for AI Worker Agents in Harness](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness)
- 组织或项目：Harness
- 发布日期：2026-07-13、2026-07-16
- 来源类型：官方技术架构文章
- 能力状态：Worker Agents 已发布；细粒度 Permission/Token Injection 仍需在目标账户验证

## 一句话结论

Harness 的生产安全思路不是要求模型始终守规矩，而是假设 Agent 进程已被攻陷，再用硬化镜像、进程/密钥/网络隔离、委托身份、短期权限子集和工具交集限制 Blast Radius。

## 可核验事实

- Worker Image 移除 Compiler、Package Manager、Setuid/Setgid 和多余能力，推荐只读 Root Filesystem。
- Agent、Credential Broker、Egress Proxy 由三个不同非特权用户运行，使用 Kernel/File Permission 隔离。
- Agent 环境只看到单次 Placeholder；真实 Secret 保留在 Broker，并仅向绑定 Host 注入；Response 还会做 Secret Scrub。
- 所有出口经过默认拒绝的 Allowlist Proxy；Cloud Metadata 和非授权 Host 应被拦截。
- Harness 自报回放一个 CVSS 9.0 攻击时，环境变量输出从 709 个含真实 Secret 降到 33 个且无可用凭据；隔离测试进入每次 Image Release Gate。
- Agent 以触发人的委托身份运行，Runtime Token 是 `parent RBAC ∩ declared grant`，单次 Run 结束后失效。
- 第三方工具有效范围是 `connector.allowedTools ∩ agent.allowedTools`，并记录 Agent、Run、Principal、Tool、参数和结果。
- 当前 Worker 文档同时注明：由 Webhook、Schedule、Artifact、Manifest 等 Pipeline Trigger 发起的 Run 还不能注入某个触发 Principal 的 scoped token；该授权链目前主要适用于存在 Principal 的手动/API 触发。

## CI/CD 相关性

- 涉及阶段：所有含 Agent Step 的阶段。
- 工具类别：Agent Runtime、身份授权、MCP Gateway、Secret 和网络隔离。
- 自主等级：为 L2—L3 提供生产边界；不直接证明 L4。
- 涉及角色：平台工程、安全、IAM、SRE、Pipeline Owner。

## 对洞察的价值

这是一套比 Prompt Approval 更接近生产控制的参考架构：把工作负载身份、委托人、运行权限和第三方 Tool Scope 分开，并把最关键边界下沉到 Kernel、Filesystem、Network 和短期 Token。

## 限制与待验证项

- 两篇文章均为厂商自述，公开材料没有独立复现或第三方红队报告。
- 文档中的细粒度 Harness Permission 受 `HARNESS_TOKEN_INJECT` Feature Flag 影响，必须在目标 Cluster 实测。
- 事件 Trigger 身份仍在 Roadmap；未验证前不能让事件驱动 Agent 继承高风险 Harness Grant 或生产动作。
- 需要验证 Cloud 与客户 Kubernetes Runtime 是否具有相同控制、DNS/IPv6/Sidecar/MCP 是否存在旁路、审计是否可导出。
- Marketplace Agent 的来源、签名、SBOM、评测和升级供应链仍需单独治理。

## 可引用判断

- 安全的 Agent Pipeline 应把“触发方式是否有 Principal、Agent 声明什么、Runtime 实际获得什么、第三方 Tool 允许什么”做成逐次求交集的授权链；没有可继承 Principal 时必须另建身份与审批模型。
