---
title: Google Cloud Agent Identity
source_id: google-cloud-agent-identity-2026-07-14
organization: Google Cloud
source_type: official-docs
published: 2026-07-14
verified: 2026-07-14
availability: mixed
confidence: high
geography:
  - united-states
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - identity
  - delegated-authorization
  - credential-broker
  - audit
company_topics:
  - google-cloud
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Google Cloud Agent Identity

## 来源

- 标题：Agent Identity overview
- 更新状态：动态官方文档，2026-07-14 核验
- 链接：[Google Cloud IAM 官方文档](https://docs.cloud.google.com/iam/docs/agent-identity-overview)
- 来源类型：官方产品文档
- 能力状态：混合；核心身份已有文档支持，Auth Manager 与多种外部认证模型为 Preview

## 一句话结论

主流云 IAM 正把 Agent 从共享 Service Account 中拆出来，赋予独立、强证明的身份，并把用户凭据留在网关而不是交给 Agent。

## 可核验事实

- 每个 Agent 获得基于 SPIFFE 的强证明身份，并与生命周期、托管资源绑定。
- Agent 可凭自身权限或代表最终用户访问工具和资源。
- 通过 Agent Gateway 时，最终用户凭据由 Auth Manager 加密、在网关解密，Agent 不接触原始凭据。
- 支持 IAM Allow/Deny、Principal Access Boundary、VPC Service Controls 与审计集成。

## CI/CD 相关性

- 涉及阶段：全部八阶段。
- 工具类别：SPIFFE、IAM、凭据代理、委托访问、审计。
- 自主等级：L2—L4 的身份基础。
- 涉及角色：Cloud IAM、安全平台、平台工程、SRE。

## 对洞察的价值

与 Uber 和 NIST 形成交叉验证：独立 Agent 身份、短期/绑定凭据、委托与 Gateway 是企业控制面的共同方向。

## 限制与待验证项

- Preview 必须按具体子功能标注，不能把整套能力都写成 GA。
- 当前支持的 Runtime 有限，动态文档可能继续变化。

## 可引用判断

- 生产 Agent 不应持有人的原始凭据；凭据应由外部代理托管，并把最终调用归因到具体 Agent 和委托人。
