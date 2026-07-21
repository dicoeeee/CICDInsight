---
title: Slide 05 - CI Self-Healing
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: draft
slide: 5
---

# CI 自愈的可信闭环是修复后复验，而不是无限重跑

> [!todo] 页面主张待定稿
> 这一页以“大能力块”而非单一公司为横向维度，用 CircleCI、Nx、Buildkite、GitHub 和 Harness 等代表实践说明可信自愈的共同作业流。

## 作业流草案

CI 失败 → 失败分类与证据结构化 → Agent 复现和归因 → 受限修复 → 原失败任务 + 外部 Oracle 复验 → Draft PR / Auto-apply / 停止升级

## 待补充

- 选择 2—3 个代表实践，不做厂商名录。
- 在 SH0—SH4 闭环等级与 L0—L4 行动权等级之间保持区分。
- 突出失败路由、重试预算、原任务复验和人工接管。

## 来源

- [[50_deepdives/cicd-self-healing/90_report|CI/CD 自愈深研]]
- [[30_summaries/stages/README#4. 编译、构建与出包|构建阶段总结]]
- [[80_presentations/agentic-cicd-company-workflows/source-map#Source Map|本页 Source Map]]
