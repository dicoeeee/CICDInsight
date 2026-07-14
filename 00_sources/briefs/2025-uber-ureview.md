---
title: Uber uReview 大规模 Agentic Code Review
source_id: uber-ureview-2025-08-12
organization: Uber
source_type: first-party-engineering-case-study
published: 2025-08-12
verified: 2026-07-14
availability: internal-production
confidence: medium-high
geography:
  - united-states
  - global-engineering
lifecycle_stages:
  - stage-1
  - stage-2
  - stage-3
tool_categories:
  - code-review
  - appsec
  - evaluation
company_topics:
  - uber
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Uber uReview 大规模 Agentic Code Review

## 来源

- 标题：uReview: Scaling GenAI-based code review at Uber
- 发布日期：2025-08-12
- 链接：[Uber Engineering 官方文章](https://www.uber.com/tr/en/blog/ureview/)
- 来源类型：大型企业第一方工程实践
- 能力状态：内部生产系统

## 一句话结论

大规模 AI 评审的关键不是多生成评论，而是用专业 Reviewer、验证、过滤和去重把低价值信号挡在开发者之前。

## 可核验事实

- 系统包含生成、过滤、验证和去重等阶段，并组合 Standards、Best Practices、AppSec 等专业能力。
- Uber 披露其覆盖每周约 6.5 万个 Diff 的 90% 以上，75% 评论被标记为有用，65% 以上得到处理。
- 已部署到六个 Monorepo，评审中位时延约四分钟。
- 团队使用周期性 Golden Benchmark 选择模型组合，而不是固定依赖单一模型。

## CI/CD 相关性

- 涉及阶段：代码评审、质量/安全、评审门禁。
- 工具类别：企业级代码评审平台、AppSec、评测。
- 自主等级：L1—L2。
- 涉及角色：开发者、Reviewer、AppSec、研发效能团队。

## 对洞察的价值

提供少见的大型企业覆盖率和交互指标，也说明平台运营需要持续评测、信号过滤和模型路由能力。

## 限制与待验证项

- 指标由 Uber 披露，且“有用/已处理”不等于缺陷逃逸率或事故下降。
- 不涉及自动合并或发布自治。

## 可引用判断

- Agentic Review 的规模化壁垒是信号治理与评测，而不是评论生成能力本身。
