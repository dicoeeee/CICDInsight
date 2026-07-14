---
title: How Microsoft Is Migrating Repositories to GitHub
source_id: microsoft-migrating-repositories-github-2026-06-03
organization: Microsoft
source_type: official-engineering-practice
published: 2026-06-03
verified: 2026-07-14
availability: ga
confidence: medium
geography:
  - global
lifecycle_stages:
  - code-review
  - testing-gates
  - build
  - release
tool_categories:
  - source-control
  - ci-pipeline
  - agent-runtime-orchestration
company_topics:
  - Microsoft
  - GitHub
  - Azure DevOps
autonomy_levels:
  - L0
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# How Microsoft Is Migrating Repositories to GitHub

## 来源

- 标题：How Microsoft is migrating repositories to GitHub
- 组织或项目：Microsoft Azure DevOps
- 发布日期：2026-06-03
- 链接：[Azure DevOps Blog](https://devblogs.microsoft.com/devops/how-microsoft-is-migrating-repositories-to-github/)
- 来源类型：官方内部工程实践
- 能力状态：正在进行中的内部迁移

## 一句话结论

Microsoft 把代码仓位置视为获得 Agentic 能力的战略选择，同时保留 Azure Boards 和 Azure Pipelines，形成 GitHub 源码与 Agent 平台加 Azure DevOps 既有流程的混合架构。

## 可核验事实

- CAP 组织覆盖约 4,000 个活跃仓库、53 个 Azure DevOps 组织。
- 文中称六个月内已迁移超过 1,600 个仓库和 3,100 名开发者，部分复杂仓库仍保留在 Azure DevOps。
- 迁移主要价值之一是更早获得 Copilot Coding Agent、Code Review 和 Agentic Workflows。
- Azure Boards 和 Azure Pipelines 通过 GitHub App 与链接语法继续使用，减少一次性流程替换。
- 迁移团队同时承担最佳实践传播和用户适应支持。

## CI/CD 相关性

- 涉及阶段：代码仓、代码评审、流水线和发布协作。
- 工具类别：代码仓战略、GitHub Agent 平台、Azure DevOps 混合集成。
- 自主等级：L0—L3。
- 涉及角色：开发者、迁移工程师、平台团队、Azure DevOps 和 GitHub 管理员。

## 对洞察的价值

这是大型公司真实平台迁移案例，表明 Agent 能力可能重新定义企业的工具边界，但演进路径通常是混合而非整体替换。

## 限制与待验证项

- 数据来自 Microsoft 自身实践，具有明确的平台战略背景。
- 文章没有给出迁移前后交付绩效和稳定性的对照指标。

## 可引用判断

- Agent 时代的平台迁移不一定从替换流水线开始，可能先迁移承载上下文和协作历史的代码仓，再逐步重构下游流程。

