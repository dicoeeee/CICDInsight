---
title: WhatsCode 大规模企业实践
source_id: meta-whatscode-2025-12-04
organization: WhatsApp / Meta
source_type: enterprise-research-paper
published: 2025-12-04
verified: 2026-07-14
availability: internal-practice
confidence: medium
geography:
  - global
lifecycle_stages:
  - security-compliance
  - testing-gates
  - post-release
tool_categories:
  - enterprise-agent-platform
  - compliance
  - incident-response
company_topics:
  - Meta
autonomy_levels:
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# WhatsCode 大规模企业实践

## 来源

- 标题：WhatsCode: Large-Scale GenAI Deployment for Developer Efficiency at WhatsApp
- 发布日期：2025-12-04；ICSE-SEIP 2026
- 链接：[arXiv:2512.05314](https://arxiv.org/abs/2512.05314)
- 来源类型：大型企业纵向实践研究
- 能力状态：内部生产实践

## 一句话结论

WhatsApp 的纵向实践显示，规模化 Agent 价值更多来自可审查的一键执行和人类接管修订，而不是追求所有任务完全自治。

## 可核验事实

- 论文覆盖 25 个月和 3,000 多个被接受变更。
- 隐私自动验证覆盖率从 15% 提升到 53%。
- 形成约 60% one-click rollout 与 40% commandeer-revise 两种协作模式。
- 论文称事故调查系统识别了 21% 生产故障的根因。

## CI/CD 相关性

- 涉及阶段：合规检查、测试门禁和故障调查。
- 工具类别：企业 Agent 平台、隐私验证、事故响应。
- 自主等级：L2—L3。
- 涉及角色：开发者、隐私工程、平台团队和生产工程。

## 对洞察的价值

这是少见的同时给出平台、流程、人机协作和量化结果的一手大型公司案例。

## 限制与待验证项

- 单一公司案例，作者承认用例选择偏差和外部可推广性限制。
- 量化结果未由独立第三方复核。

## 可引用判断

- 规模化落地的稳定形态是可接管的分级自治，而非无人值守的端到端替代。
