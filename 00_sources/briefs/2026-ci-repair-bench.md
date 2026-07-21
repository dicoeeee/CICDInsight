---
title: CI-Repair-Bench
source_id: ci-repair-bench-2026-04-29
organization: Academic research
source_type: primary-research-preprint
published: 2026-04-29
verified: 2026-07-15
availability: research
confidence: medium-high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - ci-pipeline
  - code-remediation
  - evaluation
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# CI-Repair-Bench

## 来源

- 标题：CI-Repair-Bench: A Benchmark for Repairing Continuous Integration Failures
- 发布或修订日期：2026-04-29 / 2026-05-04
- 链接：[arXiv 原始论文](https://arxiv.org/abs/2604.27148)
- 来源类型：原始研究预印本
- 能力状态：研究基准，不是产品

## 一句话结论

CI-Repair-Bench 用 567 个真实 CI 故障和原始完整 CI 复验校准通用 Agent：最佳受测模型只修复 18.9%，说明 CI 自愈必须按故障类型白名单化，不能用厂商 Demo 推导全场景成功率。

## 可核验事实

- 数据包含 567 个真实故障、103 个仓库和 12 类错误。
- 评价通过重跑原始完整 CI 判断修复是否成立，而不是只看 Agent 自述或 PR 是否创建。
- 最佳受测模型修复率为 18.9%。
- 格式/静态检查类相对容易，环境、依赖和配置问题更难。

## CI/CD 相关性

- 涉及阶段：测试门禁、编译和构建失败修复。
- 工具类别：CI Repair Benchmark、Agent Evaluation。
- 对自治的启示：通用开放式修复不适合作为默认 SH4；应先分类、回放和按任务授权。

## 对洞察的价值

它为 GitHub/Harness/Nx/CircleCI 等第一方材料提供外部能力校准，并给出更严格的成功定义：必须让原始完整 CI 通过。

## 限制与待验证项

- 当前为 arXiv 预印本，仍需同行评审与外部复现。
- 开源仓库样本不能直接外推到拥有私有知识和强标准路径的企业内网。
- 原始 CI 通过仍不等于 30/90 天无回归或业务语义正确。

## 可引用判断

- 企业不应设一个跨故障类别的“通用自愈率”；应按 Failure Taxonomy 分别测 repair@1、完整 CI 通过、人工接受、上线和长期复发。
