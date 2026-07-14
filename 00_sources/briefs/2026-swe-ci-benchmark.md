---
title: SWE-CI Benchmark
source_id: academic-swe-ci-2026-03-04
organization: Academic collaboration
source_type: research-preprint
published: 2026-03-04
verified: 2026-07-14
availability: research
confidence: medium
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - agent-evaluation
  - ci-pipeline
  - maintainability
company_topics: []
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# SWE-CI Benchmark

## 来源

- 标题：SWE-CI: Evaluating Agent Capabilities in Maintaining Codebases via Continuous Integration
- 作者：Jialong Chen、Xander Xu、Hu Wei、Chuan Chen、Bing Zhao
- 发布日期：2026-03-04
- 链接：[arXiv:2603.03823](https://arxiv.org/abs/2603.03823)
- 来源类型：原始研究预印本与 Benchmark 提案
- 能力状态：研究

## 一句话结论

SWE-CI 认为静态一次性修复基准不足以评估软件 Agent，提出把 Agent 放进跨多次提交和长期演进的 CI 循环中衡量可维护性。

## 可核验事实

- Benchmark 包含 100 个仓库级任务。
- 每个任务平均覆盖 233 天和 71 个连续提交的演进历史。
- Agent 需要经过多轮分析和修改解决任务，而非一次性生成 Patch。
- 目标从短期功能正确性扩展到动态、长期可维护性。

## CI/CD 相关性

- 涉及阶段：CI 构建、测试反馈和长期代码维护。
- 工具类别：Agent Benchmark、CI Feedback Loop 和可维护性评测。
- 自主等级：L2—L4。
- 涉及角色：Agent 研发、平台工程、质量工程和技术管理者。

## 对洞察的价值

它为报告提出了重要评测原则：Agent 是否能修复一次失败不够，还要看其变更在后续提交和重复 CI 中是否保持稳定。

## 限制与待验证项

- 当前为预印本，任务数量和仓库分布有限。
- Benchmark 表现不能直接替代企业环境的安全、合规和成本评估。

## 可引用判断

- Agentic CI/CD 的效果评估应从单次任务成功率扩展到长期可维护性和后续变更稳定性。

