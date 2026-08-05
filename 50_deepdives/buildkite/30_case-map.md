---
title: Buildkite 案例与替代关系地图
tags:
  - research/agentic-cicd
  - research/case-map
  - company/buildkite
status: complete
as_of: 2026-08-02
confidence: medium-high
---

# Buildkite 案例与替代关系地图

## 案例使用原则

- **A：采用方一手材料。** 可证明文章时间点的实际架构与问题，不外推为 2026-08-02 仍持续使用。
- **B：Buildkite 署名客户案例。** 可证明厂商公开记录的采用，不视为独立实验或当前客户合同。
- 客户指标只用于说明“存在某种收益路径”，不得作为 Buildkite 的通用性能、成本或可靠性承诺。
- 同一迁移通常同时改变 Provider、Runner、缓存、Build System、任务图和组织流程；不得进行单因素归因。

## 代表案例

| 案例 | 问题 | 采用机制 | 可支持的洞察 | 证据边界 |
|---|---|---|---|---|
| Reddit Mobile CI | 旧平台构建环境不可控、移动端缓存与并发受限、配置复杂 | Hosted XL + 自管 Kubernetes Agent Stack、Dynamic Pipeline、Git/Container Cache，迁移整个 Android/iOS CI | Buildkite 可将移动端异构算力、动态图和缓存组合在一条迁移路径中 | A，2025-07；提速同时来自缓存、容器、执行环境与流程重构；只确认至文章时点。[Reddit Engineering](https://www.reddit.com/r/RedditEng/comments/1megwf1/) |
| Canva | 数千 Job、数据移动、冷启动、非 Hermetic Step 与脆弱生成器造成长关键路径 | Buildkite 继续承担 CI 编排；Bazel/RBE、Starlark Pipeline Generator、大实例、EBS Warm-up 共同重构 | “增加 Step/VM”不等于更快；CI 编排必须与 Build Graph、Cache 和数据拓扑共同优化 | A，2024；收益不能归因给 Buildkite，反而揭示编排层不会自动修复任务语义。[Canva Engineering](https://www.canva.dev/blog/engineering/faster-ci-builds-at-canva/) |
| Rippling | 大规模并发测试成本高，希望使用 Spot，又要处理 Spot 中断 | Buildkite Control Plane + 多套 AWS Elastic CI Stack，自建 Spot outage 检测与 On-demand 切换 | 控制面/算力分离允许客户进行深度成本工程；Fleet 恢复逻辑仍可能由客户承担 | A，2023；材料较旧，文中明确当时 Buildkite 没有在重试时动态换 Queue 的内建能力。[Rippling Engineering](https://www.rippling.com/blog/how-rippling-used-spot-instances-to-save-and-scale-ci-cd) |
| Retool | Azure Pipelines 执行环境与团队需求不匹配，希望使用 Kubernetes 测试环境 | 迁移到 Buildkite，使用 Kubernetes 承载测试执行 | Buildkite 可以保留 SaaS 编排，同时将执行环境迁到团队熟悉的 Scheduler | A，2023；迁移结果受 Kubernetes 和团队运维能力共同影响，不能证明所有团队都应自管 K8s。[Retool Engineering](https://retool.com/blog/moving-retools-ci-from-azure-pipelines-to-buildkite-with-kubernetes) |
| Slack Mobile / Accessibility | 移动 CI 从 Jenkins 迁移；后续将无障碍回归纳入定时 CI | Buildkite 作为移动 CI 与回归测试入口 | Buildkite 可覆盖移动端和跨团队持续检查 | A，2022/2024；不能由旧材料确认今日全公司标准。[Mobile CI](https://slack.engineering/mobile-developer-experience-at-slack/)；[Accessibility](https://slack.engineering/automated-accessibility-testing-at-slack/) |
| REA Group | 多套 Jenkins/Bamboo Cluster 带来维护和团队创建瓶颈 | Buildkite + AWS + Docker 的中心 Agent Cluster，团队自助创建 Pipeline | 统一控制面与共享执行平台可以减少重复 CI 控制器并提高团队自主性 | B，页面/PDF 2025 更新；指标为厂商案例，缺少同期 REA 自身复核。[Buildkite case](https://buildkite.com/resources/case-studies/rea/) |
| Elastic / Kibana | 大型测试套件与 Pipeline 时间需要扩展 | 首批产品迁至 Buildkite，使用 Dynamic Pipeline 拆分测试 | Dynamic Pipeline 可成为大型测试图和产品渐进迁移的技术入口 | B，2024 案例；实际效果还依赖测试分片、Agent 容量与代码库结构。[Buildkite case](https://buildkite.com/resources/case-studies/elastic/) |
| PagerDuty | GoCD、自定义脚本和多团队自助之间存在摩擦 | 混合架构、客户网络中的 Agent、统一 Deployment Pipeline | Buildkite 的价值可以是“托管控制面 + 客户侧执行 + 团队自助”，不只是测试速度 | B，案例统计窗口到 2023-10；99% 等数字不可当作当前事实。[Buildkite case](https://buildkite.com/resources/case-studies/pagerduty/) |
| Shopify | 工程规模增长和大型 Rails/移动构建要求弹性算力 | 自管弹性 Build Agent 与 Buildkite 调度，后续覆盖移动端 | Buildkite 长期被用于大规模、自管算力的 CI 编排 | B，案例统计窗口到 2023-10；并发和 Job 数为厂商案例且已旧。[Buildkite case](https://buildkite.com/resources/case-studies/shopify/) |

## 案例共同模式

### 模式一：不是把代码交给 SaaS Runner，而是把 Scheduler 接到现有算力

Rippling、Retool、REA 和 PagerDuty 的共同点，是保留或重建客户侧执行环境，Buildkite 主要负责 Pipeline、Queue、状态和可视化。该模式适合已经有 AWS/Kubernetes/专用硬件平台能力的团队，也意味着 Agent Image、Cache、Capacity 和 Incident 仍属于企业平台职责。

### 模式二：大型 CI 的瓶颈通常不在“缺少并发”

Canva 表明过度拆 Step 会放大 Checkout、依赖下载、冷缓存和 VM Warm-up；Reddit 的收益也混合 Git/Container Cache、环境容器化和 Pipeline 重构。更准确的性能模型是：

```text
关键路径 = 排队 + Agent 启动 + Checkout/依赖 + 任务图依赖 + 最慢分片 + Artifact/Cache I/O
```

Buildkite 可以提供更灵活的图和算力，但只有任务图、Build System 和数据移动同时优化，灵活性才会转化为速度。

### 模式三：平台团队从“维护 CI Server”转向“经营 Agent Portfolio”

迁出 Jenkins/GoCD 后，Controller/插件/升级负担可能下降；新的工作变为：设计 Cluster/Queue、选择 Hosted/Self-hosted、管理 AWS/K8s Stack、治理 Dynamic Generator、观测排队与 Cache。它不是工作消失，而是工作从通用控制器运维转向负载感知的执行平台设计。

## 替代与互补关系

| 方案 | 最深抽象 | 相比 Buildkite 更强的条件 | Buildkite 更强的条件 | 关系 |
|---|---|---|---|---|
| GitHub Actions | Repo/Org 内 Workflow、Action、Runner 与 SCM 原生事件 | 代码全部在 GitHub、流程较标准、希望最低平台接入成本 | 大型异构 Fleet、运行时生成复杂 Job 图、跨工作负载的 Queue/Stack 设计 | 替代为主，也可由 GitHub 触发外部 Buildkite |
| GitLab CI | DevSecOps 平台、Runner、Parent/Child Pipeline | 代码/Issue/Security/Registry 已统一在 GitLab；Dynamic Child Pipeline 已满足需求 | 希望控制面更轻、开放 Agent、Hosted/Self-hosted 混合与专门 CI 产品 | 替代为主；动态图概念并非 Buildkite 独有 |
| Harness CI | 可视化/YAML Pipeline、Delegate、CI Intelligence 与更宽 CD 平台 | 需要一体化 CI/CD、审批、治理和多产品整合 | 把动态 Pipeline、Queue/Fleet 与自带算力作为中心模型 | 替代为主；Harness Test Intelligence 与 Test Engine 机制需按任务评估 |
| Jenkins | 完全自托管 Controller/Agent 和广泛 Plugin | 必须完全自托管控制面、已有大量成熟 Jenkins 资产 | 希望卸载 Controller/UI/升级，同时保留客户执行面 | 常见迁移来源 |
| Dagger | 类型化 Function/Module、内容 DAG 与可移植 Engine | 希望统一“一个 Job 内怎样构建/测试”，本地与多 CI 复用 | 希望统一事件、运行时 Job 图、异构资源路由和测试数据 | 互补为主；无已核验共同客户 |
| Bazel/Nix/Build System | 源码/目标依赖图、Hermetic Build、远程 Cache/Execution | 问题是构建正确性、细粒度增量与本地/远端构建图 | 问题是 CI 触发、跨 Job 编排、Queue/Fleet 与组织可视化 | 互补；Canva/Reddit 证明组合价值 |
| Kubernetes/AWS Scheduler | 底层 Pod/VM 调度、容量和故障恢复 | 只需底层计算平台，不需要 CI 状态与开发者体验 | 需要将 SCM 事件、Job 图、日志、Artifact 与团队权限映射到算力 | Buildkite Stack 建立组合边界 |

## 选择信号

### 强适配

- 构建需要 Linux、macOS、Windows、ARM、GPU 或私网等多类算力；
- Monorepo/大型测试矩阵需要按变更动态生成任务；
- 已有 AWS/Kubernetes 平台能力，希望保留算力控制但卸载 CI Control Plane；
- 移动端、大型 Bazel 或高并发测试对 Queue、Cache 和机器规格敏感；
- 平台团队愿意把 Pipeline Generator、Agent Stack 和 Test Data 当作产品维护。

### 弱适配

- 仓库规模小、Job 固定且 SCM 原生 CI 已足够；
- 团队没有能力或意愿运营 Agent Fleet，又受 Hosted Agent 区域/硬件/网络限制；
- 核心痛点是构建增量与 Hermeticity，却误把 CI Scheduler 当成 Build System；
- 需要完整自托管 Control Plane；
- 希望单一产品覆盖从代码仓、CI、CD、Feature Flag 到生产治理，且不愿组合平台。
