---
title: Slide 04 - AWS DevOps Agent
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: draft
slide: 4
---

# AWS 从生产调查向发布决策扩展

> [!todo] 页面主张待定稿
> AWS DevOps Agent 的当前可信价值主要是关联云拓扑、遥测、代码和发布信息，形成根因调查与 Mitigation Plan，而不是无人值守修复生产。

## 作业流草案

告警 / 事故 / 发布准备 → 资源拓扑 + 遥测 + 代码 + 部署上下文 → 多步调查 / 依赖分析 → RCA / Readiness / Mitigation Plan → 人工判断 → Ticket / PR / 预批准 Runbook

## 待补充

- 决定是以 Production Operations 还是 Release Management 为主流。
- 分开 GA 的生产调查与 Preview 的发布能力。
- 明确 AWS DevOps Agent 本体与外部组合自愈架构的边界。

## 来源

- [[20_summaries/companies/README#5. AWS：从生产根因分析向发布决策扩展|AWS 公司总结]]
- [[00_sources/briefs/2026-aws-devops-agent-production-operations-ga|AWS Production Operations Brief]]
- [[00_sources/briefs/2026-aws-devops-agent-release-management-preview|AWS Release Management Brief]]
- [[80_presentations/agentic-cicd-company-workflows/source-map#Source Map|本页 Source Map]]
