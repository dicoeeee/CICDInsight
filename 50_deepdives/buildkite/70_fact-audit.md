---
title: Buildkite 逐主张事实审计
tags:
  - research/agentic-cicd
  - research/fact-audit
  - company/buildkite
status: passed
as_of: 2026-08-02
confidence: high
---

# Buildkite 逐主张事实审计

## 审计结果

- **核心 Claim：** 23 项；全部包含直接支持证据、限制、置信度和审计状态；
- **正式材料不重复外部链接：** 40 个；关键架构、Dynamic Pipeline、Stacks API、Agent Stack、Test Engine、MCP/Model Provider、案例与替代关系均于 2026-08-02 复核；
- **产品状态：** 页面未明确状态的能力未擅自标 GA；Headless MCP `/direct` 保留 Preview；Agent `v4.0.0-beta.9` 保留 Pre-release，Latest Stable 为 `v3.135.0`；
- **案例口径：** 采用方自身材料与 Buildkite 厂商案例分级；旧案例不写成 2026 当前持续使用，效果不做单因素归因；
- **反例：** GitLab Dynamic Child Pipeline 证明动态图并非 Buildkite 独占；Canva 证明更多 Job/VM 可能放大 I/O 和冷启动；Self-hosted 模式明确保留客户 Agent Fleet 责任；
- **比较边界：** GitHub Actions、GitLab、Harness、Jenkins、Dagger、Bazel 以核心抽象和适用条件比较，不用功能数量排序；
- **形式检查：** Frontmatter、Wikilink 路径、Markdown diff 与状态字段通过检查。

## 主张—门禁核对

| 页面主张组成 | 直接证据 | 关键边界 | 结果 |
|---|---|---|---|
| Dynamic Pipeline 使任务图成为运行时程序输出 | `pipeline upload` 官方文档、SDK、配额与重试说明 | 动态配置非独占；Generator 必须测试和留档 | passed |
| Queue/Agent/Stack 使异构算力图可编程 | Architecture、Cluster/Queue、Stacks API、AWS/K8s Stack | Self-hosted 责任仍在客户；Hosted 与自管语义不同 | passed |
| Test Engine 将测试历史反馈到分片和状态 | Test Engine、`bktec`、Flaky Workflow | Quarantine 不等于根因修复；受数据与套餐限制 | passed |
| 三层组合是 Buildkite 的真正差异 | 上述三组机制与 Reddit/Canva/Rippling 反例 | 不写成“独有功能”或“必然更快” | passed |

## Presentation-ready 决定

**结论：通过。** 核心页面可以使用以下单一主张：

> **Buildkite 把 CI 拆成可编程的任务图、算力图和测试反馈图：本次需要哪些任务、任务落到哪里，以及历史测试如何改变下一次执行，都不再被固定在一张静态 YAML 中。**

页面必须保留三条限制：

1. Self-hosted 自由度对应 Agent Fleet 运维责任；
2. Dynamic Pipeline 不独占动态图概念，差异在组合架构；
3. Test Quarantine 管理影响但不修复根因，Agentic CI 只作为邻接能力。

## 不阻塞 Presentation 的剩余缺口

以下缺口影响采购/部署，不推翻上述页面主张：SaaS Control Plane 采购级 SLA 与灾难恢复、Hosted Agent 全区域/硬件矩阵、超大任务图独立基准、Test Engine 分片算法和 Agentic CI 自治效果。若页面转向“成熟度、ROI 或企业采购”，必须返回专题继续补证。
