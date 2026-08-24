---
title: CircleCI Chunk Autonomous CI/CD Agent
source_id: circleci-chunk-2026-01-28
organization: CircleCI
source_type: official-docs-and-changelog
published: 2026-01-28
verified: 2026-08-09
availability: beta
confidence: high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - ci-pipeline
  - testing
  - code-remediation
company_topics:
  - CircleCI
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# CircleCI Chunk Autonomous CI/CD Agent

## 一手来源

| 页面 | 日期/状态 | 直接支持的功能 |
|---|---|---|
| [Chunk setup and overview](https://circleci.com/docs/guides/toolkit/chunk-setup-and-overview/) | 页面未标日期；访问 2026-08-09；Beta | 当前入口、上下文、权限与 Beta 状态 |
| [Fix bugs faster with Chunk](https://circleci.com/blog/fix-bugs-faster-with-circlecis-chunk-ai-agent/) | 发布 2026-01-28；访问 2026-08-09 | Build History、测试、代码与 PR 交付 |
| [Chunk validates changes by running CI](https://circleci.com/changelog/chunk-now-validates-changes-by-running-your-ci-pipeline/) | 发布 2025-11-05；访问 2026-08-09 | 分支推送、Pipeline 验证和失败后继续尝试 |
| [Chunk auto-creates PRs on protected branches](https://circleci.com/changelog/chunk-now-auto-creates-prs-on-protected-branches/) | 发布 2026-03-29；访问 2026-08-09 | Draft PR、验证成功保留/失败关闭 |
| [CircleCI Changelog](https://circleci.com/changelog/) | 访问 2026-08-09 | 2026-06 Git Commit/PR 与瞬态重跑更新 |

## 一句话结论

Chunk 把流水线历史、测试结果和失败模式用于分类与修复，并可通过真实 Pipeline 验证候选、在受保护分支创建 Draft PR；这是 Beta 阶段的 CI 原生 SH3 形态，不是自动合并或生产恢复。

## 可核验事实

- Agent 连接现有 CircleCI Project，无需重写流水线配置。
- 可读取仓库、构建历史、测试结果和失败模式。
- 能诊断根因、生成修复并创建 PR。
- 可把候选推到分支并触发现有 Pipeline；验证失败时继续尝试修复。
- 受保护分支上自动创建 Draft PR；验证成功则保留，失败则关闭。
- 对瞬态/基础设施失败可请求重跑 Pipeline；Prompt 预授权时可自动执行，否则先询问。
- 需要 CircleCI GitHub App 的仓库读写权限以及创建修复 PR 的能力。
- 支持使用 CircleCI 提供或客户自带的模型密钥。

## CI/CD 相关性

- 涉及阶段：自动化测试、构建失败诊断和修复验证。
- 工具类别：CI 原生 Agent、测试与失败历史上下文。
- 自主等级：代码修复与 Draft PR 为 L2；预授权 Pipeline 重跑为受限执行，不包含 Merge/Deploy。
- 自愈完整度：代码路径 SH3；瞬态路径是确定性恢复快环，不等于根因修复。
- 涉及角色：开发者、CI 维护者、测试工程师和代码评审者。

## 对洞察的价值

该案例显示 CI 平台相对于通用 Agent 的差异化数据资产是跨多次运行的历史信号，而不仅是当前 Job Log。

## 限制与待验证项

- 当前仍为 Beta；公开材料缺少跨客户首次修复率、错误修复率和长期复发率。
- Validation Pipeline 的覆盖范围取决于客户配置，不能自动等同全部 Required Checks。
- Chunk Environment 可使用 CircleCI Context；Secret 必须按最小权限配置。
- 最终 Review、Merge、Deployment 与生产授权仍在外部。

## 可引用判断

- CI Agent 的核心竞争力之一是跨运行历史与真实 Pipeline 反馈，但验证循环仍必须受 PR、Required Checks、Secret Scope 和停止条件约束。
