---
title: Buildkite 专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - company/buildkite
status: complete
as_of: 2026-08-02
---

# Buildkite 专题问题树

## Q1：Buildkite 究竟是哪一层

- Pipelines SaaS Control Plane 与 Agent 分别负责什么？
- Self-hosted/hybrid 与 Hosted Agent 两种架构如何共存？
- Pipeline、Build、Step、Job、Cluster、Queue、Agent 的对象关系是什么？
- Agent 轮询、Job 获取、源码 Checkout、日志和 Artifact 的数据路径是什么？
- 与 Jenkins Controller/Agent、Harness Manager/Delegate、GitHub Actions/Runner 相比，责任边界真正不同在哪里？

## Q2：Dynamic Pipeline 的可编程性是否构成差异化

- `pipeline upload` 如何在运行中的 Build 里添加、替换和路由 Step？
- SDK 提供的类型化、可单测生成器与“任意语言输出 YAML/JSON”是什么关系？
- Monorepo affected graph、测试矩阵、分支路由和故障后换 Queue 如何实现？
- 运行时生成会引入哪些上传限制、重复 Step、插值、审计和调试问题？
- GitLab Dynamic Child Pipeline、GitHub Matrix/Reusable Workflow、Harness DAG 与 Jenkins Shared Library 是否能实现相同效果？

## Q3：Agent Fleet 的能力与运营成本是什么

- Cluster、Queue、Agent Query/Tag 如何把 Job 映射到 OS、架构、机型、网络和成本等级？
- Self-hosted Agent、Hosted Linux/macOS、AWS Elastic CI Stack、Kubernetes Agent Stack 分别适合什么负载？
- 同一 Pipeline 混合多种 Agent 是否改变开发团队的 Pipeline 表达？
- Warm Cache、Ephemeral Agent、Spot、GPU、Mac、私网和突发容量之间如何权衡？
- 谁负责 Agent 镜像、扩缩容、升级、Cache、生命周期、故障域和排队时间？
- Stacks API 是否使 Buildkite 从 Runner 产品升级为可扩展 Job Scheduler？

## Q4：Test Engine 是否形成测试数据反馈层

- 结构化 Test Result 如何从不同 Framework/CI 收集？
- `bktec` 如何利用历史耗时做分片与持续再平衡？
- flaky detection、label、mute、skip、Workflow、Owner 如何连接？
- Test Engine 能否减少关键路径，还是只把失败影响隐藏起来？
- 数据保留、套餐、Collector 覆盖、测试身份稳定性有哪些边界？
- 与 Harness Test Intelligence 的 changed-test selection 有何机制差异？

## Q5：扩展面是否会变成平台产品能力

- Hooks、Plugins、Pipeline Templates、SDK 分别适合哪些复用与控制问题？
- 哪些能力运行在 Agent 上，哪些由 SaaS Control Plane 强制？
- Package Registries 是 Buildkite Pipeline 的附属仓库，还是可被其他 CI 使用的独立资产面？
- 多语言 Package、OCI、Terraform Module、模型制品的元数据与存储责任如何划分？

## Q6：Agentic CI 改变了什么

- “Agent 帮助维护 Pipeline”与“Agent 在 Pipeline 中执行”如何区分？
- Skills、文档 Markdown/llms.txt、Remote MCP、Local MCP、Model Providers 各自位于哪一层？
- MCP 的 OAuth、API Token Preview 与 Read-only 路径怎样影响后台 Agent？
- Agent 能读取哪些 Build、Job、Log、Artifact、Cluster 与 Test Engine 数据？
- Model Providers 的当前 Provider 支持、产品状态和可观测范围是什么？
- Agentic CI 是 Buildkite 的核心差异，还是建立在既有编排/执行图上的邻接能力？

## Q7：案例能证明什么

- Reddit 的移动 CI 迁移中，Buildkite、缓存、Hosted Agent、Kubernetes 和组织迁移分别贡献什么？
- Canva 的案例为何说明“更多 Step/更多 VM”不等于更快 CI？Buildkite 与 Bazel 各负责哪层？
- Rippling 的 Spot 架构暴露了哪些 Queue 切换、失败恢复和自管 Fleet 复杂度？
- Retool 的 Kubernetes 迁移说明了哪些控制面/执行面权衡？
- Shopify、PagerDuty、REA、Elastic 的数字是否仍具当前性，能否单独归因给 Buildkite？

## Q8：何时应该采用

- 大规模并发、异构硬件、私网构建、移动端和 Monorepo 是否构成强适配？
- 简单仓库、完全托管偏好、没有平台团队或已经深度绑定 SCM 原生 CI 是否构成弱适配？
- Buildkite 与 Dagger、Bazel、Nix 是替代还是组合？
- 从单仓 Hosted Agent、单 Queue 自管 Agent、Dynamic Pipeline 还是 Test Engine 开始更合理？
- 6—8 周 PoC 应测哪些延迟、成本、可靠性、运维与开发体验指标？

## Q9：Presentation-ready 的证明标准是什么

- 单页主张能否由 Dynamic Pipeline + Agent Fleet + Test Engine 的机制直接证明？
- 是否避免把 Buildkite 讲成“更快的 CI”或“无限并发即无限性能”？
- 产品状态、套餐依赖、客户数字和时间点是否明确？
- 是否保留“灵活性来自客户承担更多执行面设计”的反例？
- 与 GitHub Agentic Workflows、Harness CI、Dagger 的层次差异是否清楚且不过度绝对化？
