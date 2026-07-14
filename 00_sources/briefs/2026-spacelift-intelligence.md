---
title: Spacelift Intelligence
source_id: spacelift-intelligence-2026-03-18
organization: Spacelift
source_type: official-product-blog
published: 2026-03-18
verified: 2026-07-14
availability: ga
confidence: medium
geography:
  - global
lifecycle_stages:
  - infrastructure-deployment
  - release
tool_categories:
  - infrastructure-deployment
  - policy-as-code
  - agent-runtime-orchestration
company_topics:
  - Spacelift
autonomy_levels:
  - L0
  - L1
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Spacelift Intelligence

## 来源

- 标题：Introducing Spacelift Intelligence
- 组织或项目：Spacelift
- 发布日期：2026-03-18
- 链接：[Spacelift Blog](https://spacelift.io/blog/introducing-spacelift-intelligence)
- 来源类型：官方产品博客
- 能力状态：文中称 Available Now

## 一句话结论

Spacelift 允许 Agent 直接参与基础设施工作流，但仍通过原有 Worker、Policy、Space、审批和治理控制面执行，形成 GitOps/IaC 之外的第二条受治理变更路径。

## 可核验事实

- 产品包含 Infrastructure Assistant 和自然语言部署能力 Spacelift Intent。
- Assistant 理解 Stack、State、Run 和配置，可查询并操作真实环境上下文。
- Intent 可根据自然语言直接部署基础设施，不强制先生成 Terraform 仓库变更。
- 既有 IaC、GitOps、VCS 集成、策略和审批仍保留。
- 两种路径共用同一治理和执行基础设施。

## CI/CD 相关性

- 涉及阶段：环境准备、基础设施部署和变更审批。
- 工具类别：IaC、基础设施控制面、Policy as Code、Agent 部署。
- 自主等级：L0、L1、L3。
- 涉及角色：平台工程师、DevOps、基础设施架构师和应用团队。

## 对洞察的价值

这是“Agent 不再只生成 IaC，而是在治理平台内直接表达和执行意图”的代表，会改变代码仓在部分基础设施变更中的中心性。

## 限制与待验证项

- 产品定位材料未给出权限越界、回滚和复杂变更成功率数据。
- “无需仓库”路径对审计、可复现和漂移管理的长期影响需要案例验证。

## 可引用判断

- Agent 可能为基础设施引入一条不经过传统代码制品、但仍经过相同策略和审批控制面的新变更路径。

