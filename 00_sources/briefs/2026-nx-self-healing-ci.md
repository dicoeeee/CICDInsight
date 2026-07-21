---
title: Nx Self-Healing CI
source_id: nx-self-healing-ci-2026-04-22
organization: Nx
source_type: official-docs-and-product-blog
published: 2026-04-22
verified: 2026-07-15
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - ci-pipeline
  - build-graph
  - code-remediation
company_topics:
  - Nx
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Nx Self-Healing CI

## 来源

- 标题：[AI-Powered Self-Healing CI](https://nx.dev/docs/features/ci-features/self-healing-ci)、[Self-Healing CI Now Suggests What to Auto-Apply](https://nx.dev/blog/self-healing-ci-auto-apply-suggestions)
- 组织或项目：Nx / Nx Cloud
- 关键更新日期：2026-04-22
- 来源类型：官方产品文档与官方博客
- 能力状态：Nx Cloud 正式文档能力；具体套餐、区域和自托管条件需按部署核验

## 一句话结论

Nx 把自愈权绑定到 CI Task 和 PR 分支：Agent 利用 Project Graph 与 Task Metadata 生成修复，由原失败 Task 复验，可按 Eligible/Never-fix Pattern 限制范围，并只对白名单任务开放 Auto-apply。

## 可核验事实

- `nx fix-ci` 被配置为即使前序 Task 失败也运行；全部成功时自动 No-op。
- Agent 生成修复后重跑失败 Task，并在 PR、IDE 或 Nx Cloud 提供 Apply、Reject、Apply Locally 与 Revert。
- 支持 GitHub、GitLab、Azure DevOps 和 Bitbucket 的 VCS Integration。
- Protected Branch 不生成修复；可通过 Task Pattern 指定允许和禁止修复的任务。
- `SELF_HEALING.md` 可定义 Off-limits Area、Fix Preference、Predefined Fix 和上下文。
- Auto-apply 只对组织选定的 Task 自动把验证后的修改推回 PR Branch；2026-04 增加基于实际历史的候选建议。

## CI/CD 相关性

- 涉及阶段：测试门禁、编译和构建。
- 工具类别：Build Graph、Task Orchestration、CI Failure Remediation。
- 自主等级：分析与 Patch 为 L1—L2；是否需要人批准写回取决于 Auto-apply 配置。Merge/Deploy 权仍在外部。
- 自愈完整度：默认 SH3；白名单 Auto-apply 在 PR 分支微域可达到局部 SH4。

## 对洞察的价值

这是“按任务而非按产品授权自治”的清晰样本：上下文来自 Build Graph，成功由原 Task 复验，行动面受分支和 Pattern 限制，且提供 Revert。

## 限制与待验证项

- 核心 Nx 项目开源不代表 Self-Healing CI 服务本身开源。
- 官方文档证明机制，不提供跨客户错误修复率、缺陷逃逸率和总成本。
- 只重跑失败 Task 仍不能替代仓库完整 Required Checks 和业务语义审查。

## 可引用判断

- CI 自愈的可扩展授权单位应是“任务类型 × 分支/环境 × 允许动作”，而不是给整个 Agent 永久写权。
