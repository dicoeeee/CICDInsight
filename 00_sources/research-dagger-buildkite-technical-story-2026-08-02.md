---
title: Dagger 与 Buildkite：可用于洞察PPT的技术机制预研
date: 2026-08-02
as_of: 2026-08-02
scope: Dagger and Buildkite technical mechanisms; comparison with GitHub Agentic Workflows and Harness CI
status: research-input
presentation_ready: false
confidence: medium-high
---

# Dagger 与 Buildkite：可用于洞察PPT的技术机制预研

## 结论先行

最值得讲的不是“又两个 CI 产品”，而是两种不同的**可编程 CI 抽象**：

1. **Dagger 将交付逻辑变成类型化、可组合的函数 API，并把一次调用编译为可缓存的执行 DAG。** 它解决的是“同一交付逻辑如何在本地、CI、Agent sandbox 中以同一语义复用”。
2. **Buildkite 将 CI 变成可在运行时生成的编排图，并将执行基础设施抽象为可替换的 Agent Fleet。** 它解决的是“面对异构算力、规模与复杂依赖，如何保持调度自由度”。
3. 这两层可以相邻：Buildkite 可充当事件、队列、Agent 与异构执行面的编排器；Dagger 可承担其中构建、集成测试和制品加工的可复用执行函数。公开资料只证明这种技术可组合性，**不代表二者存在已核验的大规模联合客户部署**。

以下重点刻意不以安全、可信或生产发布闭环为主，而强调可编程能力、反馈速度和平台复用能力。

## 一页 PPT 推荐主线

**标题：CI 正从“写步骤”演进为“编程执行图 + 编程算力图”**

**主张：** Dagger 把“交付动作”沉淀为可调用、可组合、可在任意环境重放的函数；Buildkite 把“何时、在哪、以什么拓扑执行”留给运行时编排和 Agent Fleet。二者共同把 CI 从固定 YAML 流程升级为可演化的平台能力。

**页面三段：**

| 层次 | 代表机制 | 可借鉴能力 |
|---|---|---|
| 交付逻辑层（Dagger） | 类型化 Module / Function API + 执行 DAG + 内容缓存 | 将构建、测试、发布逻辑做成企业内部可复用“能力件”，而不是每仓库复制 YAML |
| 执行编排层（Buildkite） | `pipeline upload` 运行时生成步骤图 + queue/agent 路由 | 用代码按变更集、测试历史、资源类型生成真正需要的 CI 图，而非维护一张最大静态图 |
| 执行基础设施层（Buildkite） | SaaS 控制面 + 自托管 / K8s / 托管 Agent 数据面 | CPU、GPU、macOS、私网和突发测试负载可采用不同队列与运行形态，而不改变上层流水线表达 |

**一句企业启示：** 把“业务交付逻辑的复用”和“运行资源的调度”解耦；先统一内部执行函数，再让编排层按变化和资源特征生成最小执行图。

## 最值得讲的五项技术

### 1. Dagger：类型化 Module / Function 取代脚本拼接

**机制。** Dagger Engine 对外提供基于 GraphQL 的通用类型系统；CLI/SDK 调用 Engine，Module 被加载后会动态扩展该会话的 API。Module 可暴露 functions、objects、checks、services、generators 与 dependencies；官方明确将 Module 定义为 API，而不是脚本。[Dagger overview](https://docs.dagger.io/)；[API internals](https://docs.dagger.io/reference/api/internals/)；[Modules](https://docs.dagger.io/next/extending/how-dagger-works/modules/)（访问：2026-08-02）。

**可讲能力。** 将 `build-image`、`run-integration-test`、`publish-sdk` 之类动作做成有参数、返回类型、文档和依赖关系的内部产品，而非各仓库 YAML 模板的文本复用。依赖 Module 的客户端绑定可由 SDK 生成，因此调用面可以随 Module API 演进。

**企业启示。** 平台团队应沉淀“可调用交付能力目录”，应用团队调用能力而非复制流水线。最适合跨语言、多仓库且构建/测试模式重复的组织。

**边界。** 类型化 API 不能自动解决测试口径、制品策略或跨团队所有权；还需要版本治理、兼容性约定和模块维护责任人。Dagger Core 为当前正式文档所述能力；该页未声明 Preview/Beta，不能据此为所有 SDK 或 Module 生态逐项宣称 GA。

### 2. Dagger：将函数调用编译为内容驱动 DAG，用 BuildKit 层缓存与 Dagger Volume 缓存减少重算

**机制。** 每个 Engine session 有 GraphQL server；核心 API 请求会转为计算结果所需的低层有向无环图（DAG），并利用缓存优化执行。缓存分为 BuildKit 实现的 layer cache，以及跨 Engine session 持久化、由 Dagger 实现的 volume cache；函数会在输入未变化时重用已有 layers。[Dagger API internals](https://docs.dagger.io/reference/api/internals/)；[Caching](https://docs.dagger.io/0.17.2/features/caching/)（访问：2026-08-02）。

**可讲能力。** 从“步骤 1 成功再执行步骤 2”的顺序思维，转向“相同输入不重算、无依赖可并行、构件按内容复用”的计算图思维。对大型依赖安装、镜像构建、矩阵测试而言，优化重心可从手工 cache key 迁移到输入边界与缓存可复用性。

**企业启示。** 先把函数输入/输出和外部副作用划清，才能获得细粒度缓存；缓存命中不是单独功能，而是正确建模交付图的副产品。

**边界。** 依赖时间、远端可变 tag、网络服务、未显式输入的宿主状态都会削弱可重用性。Dagger 官方故障排除也记录了 BuildKit 无响应和清理缓存后首跑变慢的运维现实；不能把“默认缓存”解读为所有流程必然加速。[Troubleshooting](https://docs.dagger.io/reference/troubleshooting)（访问：2026-08-02）。

### 3. Dagger：容器化 Service Binding 与 local/CI portability，使集成环境成为函数图的一部分

**机制。** Dagger Functions 可启动服务容器，并在函数之间或宿主机与函数之间连接服务。服务具备内容寻址 hostname、按需启动、去重、健康检查和不再需要时停止等行为；CLI 在需要时可自动启动 Engine。官方定位是同一自动化可在 laptop、AI sandbox、CI server 或 cloud 环境运行。[Services](https://docs.dagger.io/extending/services/)；[Dagger overview](https://docs.dagger.io/)（访问：2026-08-02）。

**可讲能力。** 把数据库、消息队列、被测服务等临时依赖装入函数调用图，减少“本地能复现、CI 不能”的环境断层；开发者可在提交前运行与 CI 相同的检查函数。

**企业启示。** 集成测试的可移植性来自“测试环境也被程序化表达”，而不是给本地和 CI 各维护一套脚本。特别适合契约测试、端到端测试、多服务联调。

**边界。** Service 只在 Dagger 可控制的容器/网络边界内具有该语义；每个独立 client session 会得到自己的服务实例，不能把它当作跨 CI 作业共享的长期环境。[Services](https://docs.dagger.io/extending/services/)（访问：2026-08-02）。

### 4. Buildkite：动态 Pipeline Upload——在运行时生成、扩展并路由执行 DAG

**机制。** Buildkite 允许构建中的脚本生成 YAML/JSON，并通过 `buildkite-agent pipeline upload` 将新步骤加入同一 build；每个生成步骤是独立 job，可根据 `agents` query 或 queue 调度到不同 Agent。生成器可用任意能输出 YAML/JSON 的语言实现，官方亦提供多语言 SDK 以获得类型化、可测试的 Pipeline 定义。[Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines)（访问：2026-08-02）。

**可讲能力。** 以变更集、模块依赖、测试历史、目标平台或资源标签为输入，在运行时生成最小任务图；例如仅测试受影响组件、把 GPU 测试路由至 GPU 队列、按测试分片数生成并行 job。它不是“YAML 模板”，而是 Pipeline 图生成能力。

**企业启示。** 对 monorepo 或异构工程，应该把 pipeline generator 当作平台代码来测试、版本化和观测；让静态入口只保留触发器，实际 DAG 由领域逻辑生成。

**边界。** 动态性会把配置错误从提交时推迟到构建中：生成的 YAML 上传失败可能在 build 已运行后发生，重试还可能产生重复步骤。官方建议生成步骤设置 `key`、失败即退出、保留上传物以便审计和本地 dry-run。[Dynamic pipelines troubleshooting](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines)（访问：2026-08-02）。

### 5. Buildkite：把控制面与 Agent Fleet 分离，并把测试结果变为调度输入

**机制（基础设施）。** Buildkite Pipelines 的混合架构把 SaaS control plane（协调、可视化、集成、API/webhook）与客户自管 Agent build environment 分离；也可使用 Buildkite 托管 Agent。Agent 可运行在本地机、云实例、容器/Kubernetes 等环境；AWS Elastic CI Stack 是包含 Docker、S3、CloudWatch 集成的 autoscaling Agent cluster，Kubernetes Agent Stack 则以 pod 执行构建。[Pipelines architecture](https://buildkite.com/docs/pipelines/architecture)；[Agent management](https://buildkite.com/docs/pipelines/best-practices/agent-management)；[Agents in AWS](https://buildkite.com/docs/agent/self-hosted/aws)（访问：2026-08-02）。

**机制（反馈）。** Test Engine 收集测试结果，也可接收非 Buildkite CI 的结果；可按历史耗时拆分测试到并行 Agent，追踪可靠性，并通过 workflows 标记/隔离 flaky test。`bktec` 可以基于 test state 拆分测试并跳过或 mute 被隔离测试；部分 test-state/自动隔离能力受 Pro/Enterprise 计划限制。[Test Engine overview](https://buildkite.com/docs/pipelines/configure/tests)；[Reduce flaky tests](https://buildkite.com/docs/pipelines/reduce-flaky-tests)；[bktec](https://buildkite.com/docs/pipelines/speed-up-builds-with-bktec)（访问：2026-08-02）。

**可讲能力。** 第一层是“计算位置可换”：同一 Pipeline 可把 macOS、GPU、私网、突发隔离任务分配到不同队列/Agent 形态；第二层是“历史测试数据反哺调度”：不只是并行更多 job，而是按实际测试负载分片，处理不稳定测试对关键路径的干扰。

**企业启示。** 将 CI 资源池从一个 runner 群升级为按 workload 设计的 Agent portfolio；将测试结果从日志附件升级为可用于分片、治理和协作的结构化数据。

**边界。** 自管 Agent/Elastic/Kubernetes Stack 换来灵活性，也要求团队负责镜像、缓存、弹性、观测和生命周期；Kubernetes 中 checkout 与 command 处于不同容器，hooks 的环境变量不能天然跨阶段传递。[Hooks on Agent Stack for Kubernetes](https://buildkite.com/docs/agent/hooks)（访问：2026-08-02）。不应将 Test Engine 的隔离能力描述成“修复 flaky test”；它管理影响和反馈，根因修复仍是工程工作。

## Dagger Cloud、Buildkite Agentic CI：可作为“下一层”，但不要喧宾夺主

| 能力 | 已核验机制 | 产品状态与PPT口径 |
|---|---|---|
| Dagger Cloud Traces / Modules | 汇集开发与 CI 中各 Engine 的 telemetry，按 module 展示 API、活动、依赖和 trace；Trace 用于查看耗时、日志和缓存状态。[Cloud docs](https://docs.dagger.io/reference/configuration/cloud/) | 文档可用；不将 2024 年发布时的 Traces Beta 标签外推为当前状态。可讲“函数图的可观测性”。 |
| Dagger Cloud Engines | 托管 Engine、自动扩缩和分布式缓存；`dagger --cloud` 将执行移至 cloud，但保留本地 context 流式可用的体验。[Dagger changelog](https://dagger.io/changelog/) | **Early Access**（2026-03 changelog）。不可作为成熟主能力承诺。 |
| Dagger Cloud Checks | Git 事件触发 `dagger check`，发现 module checks 并在托管 Engine 上执行、回写 GitHub status。[Cloud docs](https://docs.dagger.io/reference/configuration/cloud/) | **Early Access**（changelog 明示）。可作为“函数原生 CI”方向，不宜替代主线。 |
| Buildkite AI agents / Agentic steps | 一类能力帮助 Agent 生成和维护 Pipeline（skills/MCP/docs context）；另一类允许 Agent 作为 Pipeline step 运行，模型 provider 可接入日志、artifact、实时 pipeline 数据。[AI agents in Pipelines](https://buildkite.com/docs/platform/ai-agents) | 当前官方文档已提供；该页面未写明 GA/Preview，故口径为“已文档化能力，状态未在该页明确”。核心价值是把 Agent 接入已有 CI 上下文，不是重新定义底层执行模型。 |
| Buildkite Package Registries | 为 Pipelines 及其他 CI/CD 系统管理 package/artifact，支持 OCI、Helm、npm、PyPI、Terraform 等生态及自有 S3/GCS 后端。[Package Registries](https://buildkite.com/docs/package-registries) | 已文档化产品能力；适合作为“CI 邻接制品能力”，不建议占据本页中心。 |

## 与 GitHub Agentic Workflows、Harness CI 的差异

| 对象 | 核心抽象 | 最适合讲的价值 | 与 Dagger / Buildkite 的本质区别 |
|---|---|---|---|
| GitHub Agentic Workflows | Repository 内 Markdown 指令 + YAML frontmatter，编译为 `.lock.yml` 并作为 GitHub Actions 运行；以 coding agent 对仓库上下文进行推理。[GitHub Docs](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows) | “让 Agent 在已有 GitHub 事件、Issue、PR 语境中执行任务” | **Agent task 定义层**，不是 Dagger 那样的通用容器执行/函数图，也不是 Buildkite 那样的异构 Agent Fleet 编排。当前为 **public preview**。 |
| Harness CI | Pipeline Studio 提供 YAML 与可视编辑；Harness Delegate 在客户环境执行 CI 相关操作，连接 SaaS 管理面与 build infrastructure，并提供 DAG 编排、日志、分析等。[Harness CI overview](https://developer.harness.io/docs/continuous-integration/get-started/overview) | “一体化 CI 平台与企业级流水线管理体验” | 与 Buildkite 一样有控制面/客户侧执行组件，但 **Buildkite 的突出点是运行时 Pipeline graph generation 与可替换 Agent topology**；Harness 的突出点是平台化的 pipeline/stage/step 管理和更宽的交付平台整合。Dagger 则更下沉到交付函数的执行语义。 |
| Dagger | Module API、类型系统、容器执行 DAG、可移植 Engine | “一次编写交付函数，本地/CI/Agent sandbox 复用” | 它首先是**可编程交付 runtime**；可以被 GitHub Actions、Buildkite、Harness 等触发，而非必须取代它们。Cloud Checks 是向原生 CI 延伸，但目前 EA。 |
| Buildkite | 运行时 Pipeline DAG、Agent queues/fleets、测试数据层 | “按工作负载实时生成并调度 CI” | 它首先是**可编程编排与执行基础设施平台**。Agentic steps 是叠加能力，不是其全部技术故事。 |

### 适合在口头讲解时使用的“分层图”

```text
GitHub Agentic Workflows      ：Agent 用自然语言在仓库事件中做判断（任务层）
Harness CI                    ：统一管理 Pipeline、Stage、Step 与构建基础设施（平台层）
Buildkite                     ：运行时生成 Pipeline 图，并把任务投递到合适 Agent Fleet（编排/算力层）
Dagger                        ：将具体交付动作建模为类型化函数，在容器 DAG 中执行和复用（执行语义层）
```

这个图不是严格的产品栈替代关系：每一层都可能有重叠功能。它的用途是避免把“Agent 能调用 CI 工具”误讲成“已有可组合、可缓存的执行模型”，或把“能运行容器”误讲成“具备运行时 DAG 生成能力”。

## PPT 编排建议与取舍

若只能讲一页，推荐只保留以下三组证据：

1. **Dagger Functions + DAG cache**：证明 CI 逻辑可以从 YAML 迁移到类型化、可组合的计算模型。
2. **Buildkite Dynamic Pipelines**：证明编排图可在运行时、由领域逻辑生成。
3. **Buildkite Agent Fleet + Test Engine**：证明图的调度和优化依赖于异构计算与结构化测试数据。

不建议将 Dagger Cloud Checks、Cloud Engines、Buildkite Agentic CI 作为本页中心：前两项 Dagger Cloud 能力当前为 EA，Buildkite Agentic CI 更适合作为“在既有 CI 上叠加智能任务”的例子。它们可放在页脚“下一步”或演讲补充。

## 来源与核验说明

- 访问日期：2026-08-02。优先使用 Dagger、Buildkite、GitHub、Harness 官方文档与 Dagger 官方 changelog。
- 本文将“产品页面或文档存在”与“GA”严格区分；没有官方状态标记的能力写为“已文档化”，不擅自标注 GA。
- 本文为技术预研，未创建或更新 Deep Dive，**不得直接视为 presentation-ready**；正式公司页或功能页仍需对应 Deep Dive 的逐主张审计。
