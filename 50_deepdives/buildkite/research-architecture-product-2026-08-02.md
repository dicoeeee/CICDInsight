---
title: Buildkite 架构与产品机制研究底稿
topic: buildkite
research_type: primary-source-architecture-product
as_of: 2026-08-02
accessed: 2026-08-02
status: research-input
confidence: high
---

# Buildkite：把 CI 任务图与执行算力图解耦的架构

## 0. 研究摘要

**核心判断（分析推断，high）：** Buildkite 的差异化不在于“又一种 YAML CI”，而在于将 **Buildkite SaaS 控制面**、**可在运行时扩展的任务图** 与 **可由企业或 Buildkite 分别托管的执行算力图** 解耦。Pipeline 可以先执行一个生成器，再在运行中上传真实 Job 图；每个 Job 再依据 Queue 路由到不同架构、操作系统、规模或托管方式的 Agent。Test Engine 则把历史测试数据回流到下一轮任务分片与测试状态处置。

这使其尤其适合多语言、多平台、Monorepo、大型测试矩阵，或需要把 CI Runner 放在私有网络/专用硬件中的平台工程场景；它**不自动**提供构建本身的内容寻址或跨 CI 可移植执行语义（这与 Dagger 的核心不同）。

| 研究问题 | 结论 | 证据强度 |
| --- | --- | --- |
| 控制面与执行面是否分离？ | 是。Pipelines 是 SaaS 平台；Agent 可在自托管基础设施运行，也可使用 Buildkite Hosted Agents。 | 官方文档，high |
| 任务图能否在运行时改变？ | 是。任一运行中 Job 可通过 `pipeline upload` 向同一 Build 加入 YAML/JSON Steps；SDK 可生成类型化定义。 | 官方文档，high |
| 能否把不同任务送到异构算力？ | 是。Step 指定 Queue；Queue 可对应自托管或 Hosted Agent，典型维度为 CPU 架构、机器规格、OS、GPU。 | 官方文档，high |
| 是否有数据驱动的测试执行反馈？ | 是。Test Engine 收集结果；`bktec` 按历史时长分片，并用 test state mute/skip 已隔离的测试。 | 官方文档，high |
| Agentic CI 是否已经普适、模型无关？ | 否。MCP 与 Pipeline 内 Agent Step 已有公开文档；Model Provider 当前仅支持 Anthropic，且文档未给出稳定性分级。 | 官方文档，high |

## 1. 范围、状态与证据口径

- **范围：** Pipelines 控制/执行架构、Cluster/Queue/Agent/Stack、Dynamic Pipelines、Hooks/Plugins/Artifacts/Cache、AWS 与 Kubernetes 执行栈、Test Engine、Package Registries、Agentic CI。
- **时间点：** 2026-08-02；所有链接当日访问。
- **来源口径：** 仅 Buildkite 官方 Documentation、官方 Changelog 与官方开源项目入口。未使用厂商案例的性能数据作为架构结论。
- **状态口径：** 大多数文档页未标注 GA/Preview/Beta，以下称为“**当前公开文档能力，状态未标注**”，而非擅自判为 GA。唯一明确标注实验性的内容在本底稿中保留其原标签（例如 AWS Stack 的 git-mirrors experiment、EC2 Mac CloudFormation experimental）。

## 2. 第一层：SaaS 控制面与可替换执行面

### 2.1 可核验事实

1. [Self-hosted agents](https://buildkite.com/docs/agent/self-hosted) 明确将 Buildkite Pipelines 定义为 SaaS 平台；自托管 Agent 可以部署在本地服务器、AWS/GCP 等云服务或 Kubernetes。Agent 以 Cluster Token 注册到 Cluster 中的 self-hosted Queue，并等待获取 Job。**状态：当前文档能力，未标注 GA/Preview。**
2. [Hosted agents](https://buildkite.com/docs/agent/buildkite-hosted) 说明 Hosted Queue 调度 Job 时，Buildkite 启动新的 ephemeral Agent；Job 结束后虚拟化环境及该 Job 生成的数据被销毁，Cache Volume 数据例外。Linux 使用带容器化基础镜像的环境，macOS 使用按 Queue 设置的 macOS/Xcode VM。**状态：当前文档能力，未标注 GA/Preview。**
3. 同一 Hosted Agents 文档称每个 Hosted Queue 与其 Agent 均配置在一个 Buildkite Cluster 内；Cluster 自带隔离的 cache volumes、remote Docker builders、internal container registry 和 secrets。此为 Hosted 执行面的产品语义，不能外推为所有自托管 Agent 都自动具备相同隔离和缓存。
4. [Clusters overview](https://buildkite.com/docs/pipelines/security/clusters) 说明 Cluster 常按产品线或工作类型划分；不同 Cluster 的 Pipeline 默认不能互相 trigger 或访问 artifact，除非显式创建规则。Queue 常按 x86/ARM、规格、OS/GPU 等基础设施属性划分。
5. [Queues overview](https://buildkite.com/docs/agent/queues) 说明 Pipeline 的 Step 用 `agents` 属性指定 Queue；Queue 可以是企业自托管或 Buildkite Hosted。该机制使一条 Build 内不同 Step 可以被分派到不同队列。

### 2.2 架构解释（基于上述事实的推断）

```text
SCM webhook / API
       │
       ▼
Buildkite Pipelines SaaS 控制面
  ├─ Build / Step 状态机
  ├─ Pipeline YAML 校验与动态 Step 合并
  ├─ Cluster、Queue、Token、规则与可观测数据
  └─ 将可运行 Job 指派给匹配 Queue
       │
       ├──────── 自托管 Agent / AWS EC2 / K8s Pod / 专用硬件
       └──────── Buildkite Hosted ephemeral Linux 或 macOS Agent
                         │
                         ▼
                       Job 执行
```

**推断：** Buildkite 的“可编程”有两面：上游是 Job 图的生成，随后是 Job 与资源池的匹配。它不要求企业把所有执行环境迁移到一个托管 Runner，也不要求每种工作负载共用同一份基础镜像。这比静态 YAML + 单一 Runner Pool 更适合异构工程组织；代价是 Cluster/Queue 划分、镜像、缓存、弹性容量和运行成本仍由平台团队设计。

## 3. 第二层：Cluster、Queue、Agent 与 Stack 是不同抽象

| 对象 | 官方定义/职责 | 不能混同为 |
| --- | --- | --- |
| Cluster | 组织内将 Pipeline、Queue、Agent Token 归组的边界；跨 Cluster trigger/artifact 默认隔离。 | Kubernetes cluster 或单一机器池 |
| Queue | Job 的目标运行池，可为 Hosted 或 self-hosted；Step 用 `agents` 指向它。 | Scheduler 本身 |
| Agent | 获取并执行 Job 的进程/运行实例。 | 所有 Job 的长期宿主 |
| Stack | 同时能从 API 获取 Job 定义、并把 Job 转成运行 Agent 的软件调度器。 | 单个 Buildkite Agent |

### 3.1 Stack 是公开的调度扩展边界

1. [Stacks API](https://buildkite.com/docs/apis/agent-api/stacks) 将 Stack 定义为同时能从 Buildkite API 拉取/接收 Job、并把 Job 定义转换为运行 Agent 的软件进程；Stack 可理解为 Buildkite Job 的 orchestrator/scheduler。该 API 驱动 Agent Stack for Kubernetes，也面向需要大规模自定义调度的高级企业用户。
2. 同一 API 要求 Stack 注册 `key` 与 `type`（`kubernetes`、`elastic` 或 `custom`）；Stack 实现需在 Queue paused 时尊重 `cluster_queue.dispatch_paused`，不再启动新 Job。
3. [Agent Stack for Kubernetes](https://buildkite.com/docs/agent/self-hosted/agent-stack-k8s) 说明该 Stack 是 Kubernetes Controller：它通过 Agent API 观察分配给 Queue 的 scheduled Job；收到匹配 Job 后创建 Kubernetes Job，再创建包含 `copy-agent`、`imagecheck` init container、`agent`、`checkout` 及用户容器的单 Pod。v0.28.0 及之后使用 Agent REST API，早期版本使用 GraphQL API。
4. 该文档还明确：Buildkite Cluster 与 Kubernetes Cluster 无关；Agent Stack K8s v0.35.0+ 要求 Kubernetes 1.29+，因为采用 native sidecar containers。**这是版本兼容约束，不应简化为“任何 Kubernetes 都能原生运行”。**

### 3.2 技术含义（分析推断）

Buildkite 把“决定 Job 是否属于某个 Queue”的平台状态机与“怎样将 Job 物化为 EC2、Pod 或其他 Agent”的基础设施控制器分开。Kubernetes Stack 是此分层的具体实现，Stacks API 则暴露了替换/定制后半段的接口。这一设计的能力上限是企业可将 CI 调度接到已有的 K8s、容量策略或专用计算池；相应地，Stack 实现也承担队列暂停、保留、失败回写和扩缩容正确性，不能把它视为无运维的 Agent 安装器。

## 4. 第三层：Dynamic Pipelines——运行时生成 Job Graph

### 4.1 机制事实

1. [Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) 明确支持在 Build 运行时，由脚本生成 YAML 或 JSON Step，并使用 `buildkite-agent pipeline upload` 上传到**同一 Build**。生成 Step 会成为独立 Job，并按自身 `agents` query/Queue 分别调度。
2. 常见 bootstrap 模式是初始 Pipeline 只运行一个生成器；生成器读仓库结构、变更或此前 Step 写入的 metadata 后，再上传实际任务图。`--replace` 只移除 pending Step，不影响已运行/已完成 Job。
3. 生成器可以是输出 YAML/JSON 的任意语言；[Buildkite SDK](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines/sdk) 为 JavaScript/TypeScript、Python、Go、Ruby、C# 等提供可类型化、可单测的 Pipeline 定义生成方式。
4. 官方文档给出的默认配额为每次 upload 500 Jobs、每 Build 500 次 upload、每 Build 4,000 Jobs。大图需要拆分多次 upload；控制面每次都解析、校验和合并。
5. Dynamic Pipeline 输出仅在运行时产生，不能只依赖提交前 YAML 审查。官方建议本地用 `pipeline upload --dry-run` 校验、在生产 Build 上传生成 YAML artifact；并提示 retry 的 upload Step 可能重复添加 Step，要求生成的 Step 设置 `key`，或在适合的整图 bootstrap 中用 `--replace`。

### 4.2 静态条件与动态生成的实质区别（分析推断）

```text
静态 YAML：                    Dynamic Pipeline：
预先声明 N 个任务              先执行小型 generator
    │                           │
大量 if / skipped job           读取 changed files / dependency graph / metadata
    │                           │
固定任务图                     上传本次真正所需的 Job graph
                                │
                                每个 Job 依 Queue 到不同算力
```

动态能力的关键不是“用脚本拼 YAML”，而是 **在控制面运行中扩展任务图**。这允许 affected-service、平台矩阵、测试分片、失败后的替代 Queue 或集中规则模板以实际 Build 上下文决定任务集合。它也引入新工程对象：生成器本身、生成产物、去重键、失败/重试语义及 Job 数量上限。因此“动态”不能等价为“零治理”：官方文档特别指出，来自 fork 的脚本能上传任意 Step；Kubernetes 中 upload 若可注入使用高权限 ServiceAccount 的 Step，会形成提权路径。Pipeline signing 能阻止未签名 Step 通过 upload 注入。

## 5. 第四层：Agent 生命周期扩展、Artifacts 与 Cache

### 5.1 Hooks 与 Plugins

- [Plugins](https://buildkite.com/docs/pipelines/integrations/plugins) 是附加到 command Step 的自包含能力，通过十个 Job lifecycle hooks 中的一项或多项修改环境、checkout、命令执行、artifact 处理或清理。
- 同一文档指出 Plugin 在 step-level `if` 判断前运行；要条件化执行 Plugin，可用带条件的 group Step 或 Dynamic Pipeline。多个 Plugin 则按 hook 和配置顺序运行。

**洞察（分析推断）：** Plugin 的复用单位是 **单个 Job 的生命周期横切逻辑**；Dynamic Pipeline 的复用单位是 **整张或部分 Job 图的生成逻辑**。二者可组合，但不应互相替代：把服务依赖分析塞进 Plugin 会掩盖 Job 图；把 Docker/secret checkout 等十个生命周期操作复制到生成器中则会失去可治理的横切扩展点。

### 5.2 Artifact 与 Cache 的语义不同

- [Caching](https://buildkite.com/docs/pipelines/best-practices/caching) 将 Build Artifact 描述为带元数据与下载 URL、按 Artifact Storage Policy 保留、可按路径/Job/Build/API 获取的耐久 Build 输出；它可在后续 Step 或 Build 中复用，但不等于专用 Cache。
- 同一文档将 Hosted Cache Volume 描述为 pipeline-scoped、跨 Step 的 best-effort 加速器：Linux 使用本地 NVMe、macOS 使用 sparse bundle；只在 Job 成功后更新，并为并发 Job fork。因此它是非确定性的，不应成为唯一事实源。

**技术含义（分析推断）：** Buildkite 的默认优化哲学是把“可审计、可追溯的数据交接”与“为速度而生、允许 miss 的本地/近端重用”分开。这使大规模 CI 可以同时追求速度与可回放性，但不提供 Dagger 那类由执行引擎统一管理的内容寻址函数缓存；缓存命中高度依赖 Queue、镜像、存储位置与用户的 key 策略。

## 6. 第五层：两种自托管弹性执行栈

### 6.1 Elastic CI Stack for AWS

1. [Elastic CI Stack for AWS overview](https://buildkite.com/docs/agent/self-hosted/aws/elastic-ci-stack) 说明该 Stack 在用户 AWS 账户中创建 private、autoscaling Buildkite Agent Cluster，以 Auto Scaling Group 和 Launch Template 为核心；可将大型测试并行到数百节点。
2. 功能包括可配置 instance size、每实例 Agent 数、Spot bid、按 Build activity 自动伸缩、Docker/Compose、S3 secret storage、CloudWatch 日志/指标与滚动更新；支持 Linux/Windows。页面明确将 git-mirrors 标记为 experiment。
3. [Architecture](https://buildkite.com/docs/agent/self-hosted/aws/elastic-ci-stack/architecture) 说明扩缩容使用每分钟运行的 Lambda，根据 Buildkite 需求指标调节 ASG，而不是 AWS 资源利用率 target tracking；Agent Token 存于 Parameter Store SecureString，实例启动时取回并注册。
4. Stack 设计要求 Build Step 假设机器无状态，借助 metadata、artifact 或 S3 交接。**状态：当前公开文档能力，页面未标 GA/Preview；EC2 Mac CloudFormation 模板在 AWS 文档中单独标为 experimental。**

### 6.2 Agent Stack for Kubernetes

Kubernetes 路径不是“在容器里常驻一个 agent daemon”，而是控制器在 API 返回匹配 Job 后为 Job 创建 Pod。此架构可让 Job Pod 的容器、镜像和资源规格随 Pipeline Job 定义而变，并借 Kubernetes 原生调度/扩缩容体系承载运行面。代价是要处理 Kubernetes 版本、PodSpec、镜像预拉取、workspace volume、控制器版本和队列 token 这些新的运行约束。

### 6.3 两者如何取舍（分析推断）

| 维度 | Elastic CI Stack AWS | Agent Stack Kubernetes |
| --- | --- | --- |
| 基础资源单位 | EC2 ASG 实例，可配置每实例 Agent 数 | 每个匹配 Job 物化为 Kubernetes Job/Pod |
| 弹性信号 | Buildkite job demand 的 Lambda 轮询 | K8s Controller 获取 Agent API 中 scheduled Job；实际 Pod/集群容量由 K8s 体系承接 |
| 最适合 | 已以 AWS VM/AMIs、Docker、CloudWatch 为运维标准的团队 | 已以容器镜像、PodSpec、K8s 容量治理为运维标准的团队 |
| 主要约束 | AWS 资源、ASG 冷启动、共享无状态 Step 设计 | K8s 1.29+（v0.35+）、控制器/Pod/镜像治理 |

## 7. 第六层：Test Engine 将测试历史转成下一次执行决策

1. [Test Engine overview](https://buildkite.com/docs/pipelines/configure/tests) 说明 Test Engine 收集 Pipeline Job 的测试结果，并可跟踪、分析和操作任意测试框架的结果；它也接受非 Buildkite CI 的结果。
2. [bktec](https://buildkite.com/docs/pipelines/speed-up-builds-with-bktec) 会按 Test Engine 的历史时长数据自动分片、持续自动再平衡；文档以 16 分钟累计测试在四个均衡分片上约 4 分钟完成的**示意**说明机制，并非承诺企业实际节省 6 分钟。
3. 同一文档说明 `bktec` 根据 test state mute 或 skip 已隔离测试。mute 后仍执行并上报结果但不计入 Build 成败；skip 则不执行。官方推荐 mute，以便可靠性恢复后能重新启用。
4. [Reduce flaky tests](https://buildkite.com/docs/pipelines/reduce-flaky-tests) 说明 Workflow 可选择检测 monitor，自动添加 flaky 标签、通知负责方，并可自动取消标签/恢复 test state。`bktec` 和分片能力面向 Pro/Enterprise；test state 面向 Enterprise。**状态：当前文档能力，未标 GA/Preview；受套餐限制。**

**闭环边界（分析推断）：** 这里存在的是 CI 内部的“测试事实 → 下次分片/隔离策略”的反馈环，而非生产发布后的闭环控制。更快的分片不证明测试更有效；mute/skip 能减少干扰，却也需要组织以 owner、恢复阈值和退出治理防止问题长期被掩盖。

## 8. 第七层：Package Registries 扩展到制品分发面

[Package Registries](https://buildkite.com/docs/package-registries) 可管理来自 Buildkite Pipelines 或其他 CI/CD 应用的 artifacts/packages；Registry 可存储 package 与 OCI image、Terraform module 等 package-like 格式，并暴露版本、内容、校验和、依赖等元数据。官方列举生态包括 APK、OCI、Deb、Helm、Maven/Gradle、npm、NuGet、PyPI、RPM、RubyGems 和 Terraform modules，支持 AWS S3/GCS 私有存储后端以及 REST、GraphQL、Webhook 接口。

**判断（分析推断）：** 这让 Buildkite 从“触发构建的编排层”向“构建输出的注册与消费层”延伸，但不能仅凭产品同属 Buildkite 就假定 artifact provenance、策略扫描或发布审批自动完成。当前页没有将这些制品控制能力说明为一套端到端 release governance。

## 9. 第八层：Agentic CI 的两条接入路径

### 9.1 Agent 使用 Buildkite：Skills、MCP 与文档上下文

[AI agents in Pipelines](https://buildkite.com/docs/platform/ai-agents) 描述了 Buildkite 的 Skills、MCP Server 与 docs-as-context：MCP Server 通过 MCP 将 coding agent 连接到 Buildkite REST API，可实时 inspect build state、read logs、trigger runs，并根据 live data 迭代 Pipeline configuration。文档同时提供页面 Markdown 与分区 `llms.txt`，便于 Agent 载入产品上下文。

### 9.2 Buildkite 在 CI 中运行 Agent：Model Providers 与 Plugin

1. 同一文档说明 Model Provider 可直接接入 Pipeline Step，使 Agent 使用 Build logs、artifacts、security policies 与实时 Pipeline data；Agent Step 使用现有 `$BUILDKITE_AGENT_ACCESS_TOKEN`，Buildkite hosted token 仅面向 Pro/Enterprise，或可 BYO token。
2. [Model providers overview](https://buildkite.com/docs/apis/model-providers) 说明只有 Organization Admin 可以连接 Provider；**当前只支持 Anthropic models**。通过平台配置后，Agent 以现有 Job Token 调用 `$BUILDKITE_AGENT_ENDPOINT/ai/anthropic`，并可在组织 Usage 页面查看用量。

**边界判断（事实 + 推断）：** MCP 让 Agent 获得 Build 观察与触发接口，Model Provider 让模型调用嵌入 Job 运行；二者均是已文档化集成，不等于 Agent 获得代码合入、制品发布或生产部署的默认授权。实际副作用仍由 Job Token、组织配置、Pipeline Step、Queue 所处网络和外部系统凭据共同决定。官方尚未公开给出跨模型支持路线、Agent Step 成功率、成本控制、最大轮次或自愈效果基准，故这些不能进入正式成熟度结论。

## 10. 对 Dagger 的精确区分（分析推断）

| 维度 | Buildkite | Dagger |
| --- | --- | --- |
| 最小可编程单位 | Pipeline Step/Job 图、Queue 和 Agent 生命周期 | 类型化 Function/Module 与其执行 DAG |
| 主要优化问题 | 本次 Build 应生成哪些 Job、把它们调度到哪里、历史测试如何回流 | 一个交付函数及其输入/依赖如何增量、可组合地求值 |
| 执行可移植性 | 可以在 Buildkite Hosted 或企业自托管环境运行；环境策略由 Queue/Stack 管理 | Function 可由 Engine 在本地或多种 CI 调用，执行语义由 Engine 管理 |
| 最强共同点 | 均能用代码替代部分静态 YAML，并通过并发/缓存缩短反馈 | 同左 |
| 不能互推的结论 | Dynamic Pipeline 不等于内容寻址计算图；Queue 路由不等于本地/CI 执行函数一致 | Function DAG 不等于跨硬件队列调度与测试历史优化 |

因此二者存在互补组合：Buildkite 负责触发、运行时 Job 图、资源路由和结果反馈；某个 Job 内调用 Dagger 负责构建/测试逻辑的可组合执行。但“技术可组合”不是“某企业已经同时采用”的证据。

## 11. 可进入专题/汇报的结论与证据缺口

### 可以使用的正式主张

1. **Buildkite 将 CI 拆为可编程任务图与可编程执行资源图。** Dynamic Pipelines 在运行时加入 Job，Queue/Cluster/Stack 决定其执行落点；证据强度 high。
2. **Buildkite 的扩展核心不是仅有 Plugin，而是三层扩展：Job 生命周期 Hook、运行时 Pipeline 生成、执行 Stack/Scheduler。** 三层分别处理横切行为、任务图和资源物化；证据强度 high。
3. **Test Engine 让测试结果成为调度输入，而非仅是 Build 的终态。** 历史时长用于自动分片，test state 支撑自动隔离与恢复；证据强度 high，效果量不得泛化。
4. **Agentic CI 已形成“Agent 调 CI”与“CI 运行 Agent”两条能力路径，但产品覆盖仍有限。** MCP 与 Anthropic-only Provider 均有当前公开文档；不能宣称通用多模型自治 CI；证据强度 high。

### 不应使用或需补证的主张

- “Buildkite 自带全自动弹性和零运维”：不成立。自托管 Queue、Stack、AWS/K8s 的容量与运行边界仍需要企业维护。
- “Dynamic Pipeline 自动更快”：不成立。它减少无关 Job 的潜力成立，实际耗时还受 generator、控制面 upload、任务粒度、队列供应与缓存影响。
- “Test Engine 隔离 flaky test 等于修复 flaky test”：不成立。官方机制是检测、标签、通知、mute/skip 与恢复，不是根因修复。
- “MCP/Model Provider 即拥有发布权或生产闭环”：不成立。公开文档只证明 Build/Log/Trigger 和 Pipeline Step 的接入面，未证明默认发布授权或生产结果闭环。
- Package Registries 的 GA/Preview 标识、可用区域与全生态协议差异：本轮页面未见明确产品状态，需要逐项复核后再进入采购成熟度判断。

## 12. 来源账本

| 来源 | 类型 | 发布日期 | 访问日期 | 用途 |
| --- | --- | --- | --- | --- |
| [Self-hosted agents](https://buildkite.com/docs/agent/self-hosted) | 官方 Docs | 未标注 | 2026-08-02 | SaaS + 自托管执行面 |
| [Hosted agents](https://buildkite.com/docs/agent/buildkite-hosted) | 官方 Docs | 未标注 | 2026-08-02 | ephemeral hosted execution 与缓存 |
| [Clusters overview](https://buildkite.com/docs/pipelines/security/clusters) / [Queues overview](https://buildkite.com/docs/agent/queues) | 官方 Docs | 未标注 | 2026-08-02 | 隔离、Queue 路由与异构资源 |
| [Stacks API](https://buildkite.com/docs/apis/agent-api/stacks) | 官方 API Docs | 未标注 | 2026-08-02 | Stack/Scheduler 扩展边界 |
| [Agent Stack for Kubernetes](https://buildkite.com/docs/agent/self-hosted/agent-stack-k8s) | 官方 Docs + OSS 入口 | 未标注 | 2026-08-02 | Kubernetes controller/Pod 架构与版本约束 |
| [Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) / [SDK](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines/sdk) | 官方 Docs | 未标注 | 2026-08-02 | runtime Job graph、配额、验证、SDK |
| [Plugins](https://buildkite.com/docs/pipelines/integrations/plugins) / [Caching](https://buildkite.com/docs/pipelines/best-practices/caching) | 官方 Docs | 未标注 | 2026-08-02 | lifecycle hooks、artifact/cache 语义 |
| [Elastic CI Stack AWS](https://buildkite.com/docs/agent/self-hosted/aws/elastic-ci-stack) / [Architecture](https://buildkite.com/docs/agent/self-hosted/aws/elastic-ci-stack/architecture) | 官方 Docs | 未标注 | 2026-08-02 | EC2 ASG/Lambda/无状态运行模型 |
| [Test Engine](https://buildkite.com/docs/pipelines/configure/tests) / [bktec](https://buildkite.com/docs/pipelines/speed-up-builds-with-bktec) / [flaky tests](https://buildkite.com/docs/pipelines/reduce-flaky-tests) | 官方 Docs | 未标注 | 2026-08-02 | 测试数据反馈、分片与隔离 |
| [Package Registries](https://buildkite.com/docs/package-registries) | 官方 Docs | 未标注 | 2026-08-02 | 制品/包管理扩展面 |
| [AI agents in Pipelines](https://buildkite.com/docs/platform/ai-agents) / [Model providers](https://buildkite.com/docs/apis/model-providers) | 官方 Docs | 未标注 | 2026-08-02 | MCP、Agent Step、Anthropic-only 限制 |
