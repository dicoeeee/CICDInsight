---
title: Sonar Foundation Agent
source_id: sonar-foundation-agent-2025-11-14
organization: Sonar
source_type: official-product-blog
published: 2025-11-14
verified: 2026-07-14
availability: preview
confidence: medium
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
tool_categories:
  - code-quality
  - security
  - code-remediation
company_topics:
  - Sonar
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Sonar Foundation Agent

## 来源

- 标题：Introducing Sonar Foundation Agent
- 组织或项目：Sonar
- 发布日期：2025-11-14
- 链接：[Sonar Blog](https://www.sonarsource.com/blog/introducing-sonar-foundation-agent/)
- 来源类型：官方产品与研究博客
- 能力状态：早期 Agent 能力；后续已演进为 Sonar Vortex 相关产品线

## 一句话结论

Sonar Foundation Agent 把确定性 SonarQube Finding 作为修复任务和边界，让 Agent 只针对已验证问题生成 PR，减少通用 Agent 自行猜测问题的空间。

## 可核验事实

- Agent 自动检测并修复代码质量和安全问题，但修复目标由 Sonar 分析引擎提供。
- 输出通过 PR 交付并由人类决定是否接受。
- 早期场景包括硬编码 Secret、Null Check 和常见安全反模式。
- 官方发布 SWE-bench Verified、平均成本和耗时数据，但这些是厂商自测。

## CI/CD 相关性

- 涉及阶段：代码评审、静态检查、安全和修复门禁。
- 工具类别：代码质量引擎、Agent 修复和 PR Workflow。
- 自主等级：L1—L2。
- 涉及角色：开发者、AppSec、代码质量负责人和评审者。

## 对洞察的价值

该案例进一步验证“确定性发现器 + 概率修复 Agent + 人审 PR”的混合模式。

## 限制与待验证项

- 产品名称和架构在 2026 年继续演进，主报告应使用最新产品状态。
- Benchmark 数据不能直接代表企业仓库效果。

## 可引用判断

- 可靠的修复 Agent 往往不会自行决定所有问题，而是消费成熟分析器提供的确定性 Finding。

