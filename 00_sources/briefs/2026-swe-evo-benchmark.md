---
title: SWE-EVO Benchmark
source_id: academic-swe-evo-2026-05-22
organization: SWE-EVO research collaboration
source_type: research-preprint
published: 2026-05-22
verified: 2026-07-14
availability: research
confidence: medium
geography:
  - global
lifecycle_stages:
  - testing-gates
  - version-management
tool_categories:
  - agent-evaluation
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

# SWE-EVO Benchmark

## 来源

- 标题：SWE-EVO: Benchmarking Coding Agents in Long-Horizon Software Evolution Scenarios
- 发布：2025-12-20 首版，2026-05-22 v6
- 链接：[arXiv](https://arxiv.org/abs/2512.18470)；[开源仓库](https://github.com/SWE-EVO/SWE-EVO)
- 来源类型：原始研究预印本与 MIT 开源 Benchmark
- 能力状态：研究

## 一句话结论

跨版本、跨文件且带大量回归测试的软件演进任务，仍显著难于单 Issue 修复，不能把 SWE-Bench 类成绩直接外推为生产 CI 自治能力。

## 可核验事实

- 48 个任务来自 7 个成熟 Python 项目的发布说明和版本历史。
- 每项任务平均涉及 21 个文件，并由平均约 874 个测试验证。
- v6 报告 GPT-5.4 与 OpenHands 组合完成率为 25%。

## CI/CD 相关性

- 涉及阶段：测试与门禁、版本演进。
- 工具类别：Agent Benchmark、长期回归评测。
- 自主等级：L2—L4。
- 涉及角色：平台工程、质量工程、Agent 评测团队。

## 对洞察的价值

它支持将“跨版本零回归”和“部分完成度”纳入 Agentic CI/CD 评测，而非只看当前提交是否通过测试。

## 限制与待验证项

- 样本仅 7 个 Python 项目；离线重建任务不等于真实生产流水线。
- 结果会随模型和 Agent 脚手架快速变化。

## 可引用判断

- 企业应以长周期回归稳定性设定自治上限，不能用单次修复分数替代发布风险评估。
