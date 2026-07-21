---
title: Slide 03 - GitLab Duo Agent Platform
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: draft
slide: 3
---

# GitLab 用统一 DevSecOps 上下文组织 Agent Flow

> [!todo] 页面主张待定稿
> GitLab 的差异化在于将需求、代码、Pipeline、安全和治理放在同一数据面，减少 Agent 跨工具拼接上下文的成本。

## 作业流草案

Issue / MR / Pipeline 失败 / SAST 发现 → 全生命周期上下文 → Duo Agent / Flow → Review / Fix CI / SAST 修复 → Pipeline 与安全 Gate → MR / Approval / Audit

## 待补充

- 从 Review、Fix CI 或 SAST 中选定一条主作业流。
- 区分 Duo Agent Platform GA 与部分 Flow / Orbit 预览能力。
- 自托管与 BYOM 是否需要进入页面主叙事。

## 来源

- [[20_summaries/companies/README#3. GitLab：一体化上下文与治理控制面|GitLab 公司总结]]
- [[00_sources/briefs/2026-gitlab-duo-agent-platform|GitLab Duo Agent Platform Brief]]
- [[80_presentations/agentic-cicd-company-workflows/source-map#Source Map|本页 Source Map]]
