---
title: Harness AI Test Automation
source_id: harness-ai-test-automation-2026-07-06
organization: Harness
source_type: official-docs-and-cases
published: 2026-07-06
verified: 2026-07-16
availability: controlled
confidence: medium-high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - release
  - post-release
tool_categories:
  - testing
  - ci-pipeline
  - agent-runtime-orchestration
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

# Harness AI Test Automation

## 来源

- 标题：AI Test Automation Overview、Intent-driven Testing 及客户案例
- 组织或项目：Harness
- 更新日期：2026-07-06
- 链接：[产品文档](https://developer.harness.io/docs/ai-test-automation/get-started/overview/)、[Intent-driven Testing](https://developer.harness.io/docs/ai-test-automation/get-started/intent-driven/)、[Gameopedia](https://www.harness.io/case-studies/gameopedia)、[Wasimil](https://www.harness.io/case-studies/wasimil)、[Siemens Healthineers](https://www.harness.io/case-studies/siemens-healthineers)
- 来源类型：官方产品文档与第一方客户案例
- 能力状态：AI Test Copilot 在平台总览为 GA；完整模块仍需销售/团队开通

## 一句话结论

Harness 将自然语言测试意图转成可执行的浏览器/API 操作，以 DOM、截图和语义断言验证结果，并用 Smart Selector 和学习机制减少 UI 漂移导致的维护。

## 可核验事实

- Intent Test 由模型分解步骤，探索 Application Knowledge Base，生成可执行命令，并使用 DOM、Screenshot、Visual/Semantic Assertion 验证。
- 支持 Test、Suite、Run、Reusable Task、Environment、Tunnel 和 Harness Pipeline 集成。
- 测试在隔离 Kubernetes Pod 中运行，官方称不持久保存用户数据；文档描述可扩展到并行执行。
- 产品明确宣传 Self-healing、Smart Selector 和 UI 变化适配；未来能力列表中的自主测试生成等不能写成当前能力。
- Harness 案例称：Gameopedia 维护下降 40% 以上；Wasimil 测试失败率从约 50% 降到不足 10%；Siemens Healthineers PoC 将场景创建从数日降至不足 1 小时。

## CI/CD 相关性

- 涉及阶段：自动化测试、质量门禁、发布前后验证。
- 工具类别：意图测试、浏览器执行、视觉/语义 Oracle、Pipeline Gate。
- 自主等级：沙箱内 L3；生产发布判断仍需独立 Gate。
- 涉及角色：QA、开发者、测试平台、发布管理者。

## 对洞察的价值

这是 Harness 公开材料中量化案例最具体的 Agent 产品线，展示“意图 → 动作 → 浏览器事实 → 学习”的相对完整闭环，也证明测试比生产发布更适合率先扩大自治。

## 限制与待验证项

- 案例均由 Harness 选择和发布，不能视为独立对照实验或行业平均。
- 自愈可能错误选择元素或掩盖业务回归；需统计 False Pass、缺陷逃逸、长期漂移和业务断言覆盖。
- 产品总览 GA 与完整模块受控开通并存，采购时必须核对账户、Region 和 SKU。
- 同一 Agent 不应同时修改测试和用该测试证明修复成功。

## 可引用判断

- 测试 Agent 的真实成熟度不应只看维护时间下降，还要看 False Pass、缺陷逃逸和长期漂移；自愈 Selector 不能成为关键交易唯一 Oracle。
