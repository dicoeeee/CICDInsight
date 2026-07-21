---
title: Harness Code Quality Agents
source_id: harness-code-quality-agents-2026-07-02
organization: Harness
source_type: official-docs
published: 2026-07-02
verified: 2026-07-16
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - testing-gates
  - build
tool_categories:
  - code-review
  - testing
  - ci-pipeline
company_topics:
  - Harness
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Harness Code Quality Agents

## 来源

- 标题：Code Quality Agents
- 组织或项目：Harness
- 发布或更新日期：2026-07-02
- 链接：[Harness Developer Hub](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)
- 来源类型：官方产品文档
- 能力状态：正式文档能力

## 一句话结论

Harness 用三个专业 Agent 把 PR 评审、测试生成和 CI 失败修复连接成“发现—修改—验证—提交 PR”的工程闭环。

## 可核验事实

- Code Review Agent 分析 PR diff 并直接发布评审意见。
- Code Coverage Agent 生成测试来提升覆盖率，并以 PR 或报告交付。
- AutoFix Agent 分析 CI 失败与代码变更，生成修复、验证构建并创建 PR。
- 三类 Agent 都运行在流水线触发链路中，输出由开发者审查和合并。
- 这三个专项 Agent 不应自动假设与通用 Worker Agent 使用完全相同的 Runtime、模型和权限实现，采购时需分别核验。

## CI/CD 相关性

- 涉及阶段：代码评审、测试与门禁、CI 构建恢复。
- 工具类别：PR 评审、测试生成、流水线自愈。
- 自主等级：L1—L3；执行修复和验证，但合并由人控制。
- 涉及角色：开发者、测试工程师、CI 平台维护者、代码评审者。

## 对洞察的价值

这是 2026 年最清晰的“专业 Agent 群”案例之一，显示单个通用 Agent 正被拆成围绕质量结果负责的专业角色。

## 限制与待验证项

- 缺少不同语言、仓库规模和失败类型下的独立成功率数据。
- “自动修复”最终仍通过 PR 交付，不等于无人值守合并。
- Agent 不应修改或跳过用来证明其修复成功的原始 CI Gate。

## 可引用判断

- CI 自愈的现实形态通常不是直接修改主分支，而是让 Agent 诊断、修复、重跑验证并提交可审查变更。
