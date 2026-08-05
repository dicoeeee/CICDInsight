---
title: Buildkite Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/buildkite
status: complete
as_of: 2026-08-02
confidence: high
---

# Buildkite Claim—Evidence—Gap Matrix

## 证据标记

- **A：** Buildkite 当前官方文档、官方开源仓库、Changelog，可证明机制、接口、限制和明确状态。
- **B：** 采用方自己的工程材料，可证明该公司在文章时间点的架构、问题和结果。
- **C：** Buildkite 署名客户案例，只能证明厂商记录的采用和结果。
- **分析：** 主 Agent 基于多个事实形成的架构、采用或比较判断，不冒充来源原话。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| BK-C01 | Buildkite Pipelines 的核心架构是 SaaS Control Plane 与 Agent 执行面分离 | [Pipelines architecture](https://buildkite.com/docs/pipelines/architecture) 明确区分 SaaS 编排与客户 Agent | Hosted Agents 模式由 Buildkite 同时提供执行环境；不能把所有 Buildkite 使用都写成 BYOC | high | passed |
| BK-C02 | 同一组织乃至 Pipeline 可以混合 Self-hosted 与 Hosted Agent | [Agent management](https://buildkite.com/docs/pipelines/best-practices/agent-management) 与 [Pipelines advantages](https://buildkite.com/docs/pipelines/advantages/buildkite-pipelines) | 不同 Agent 的网络、镜像、Cache、性能和身份并不会自动一致 | high | passed |
| BK-C03 | Self-hosted Agent 是开源、跨平台的 Job Runner，由客户负责更新和执行环境 | [Agent repository](https://github.com/buildkite/agent)；[Self-hosted agents](https://buildkite.com/docs/agent/self-hosted) | Agent 开源不等于 SaaS Control Plane 可自托管；客户仍依赖 Buildkite API 和账户 | high | passed |
| BK-C04 | Cluster 与 Queue 把 Pipeline/Agent 隔离和工作负载路由结合起来 | [Clusters](https://buildkite.com/docs/pipelines/security/clusters)；[Queues](https://buildkite.com/docs/agent/queues) | Buildkite Cluster 不是 Kubernetes Cluster；Queue 设计不当会造成容量碎片和排队 | high | passed |
| BK-C05 | Dynamic Pipeline 能在正在运行的 Build 中生成并上传新 Step，每个 Step 独立调度 | [Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) | 上传受 Job/Upload/Build 限额约束；生成器失败发生在 Build 已启动之后 | high | passed |
| BK-C06 | Buildkite SDK 让 Dynamic Pipeline 生成器获得类型化和可测试 API，但生成器也可使用任意输出 YAML/JSON 的语言 | [Buildkite SDK](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines/sdk)；[Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) | 类型安全只覆盖 SDK 支持的结构，不证明生成的业务图正确或成本最优 | high | passed |
| BK-C07 | Dynamic Pipeline 的价值是按运行时上下文生成最小图，而不是简单 YAML 模板 | 官方文档给出按目录、分支、上下文生成及换 Queue 重试模式；[Bazel dynamic pipeline tutorial](https://buildkite.com/docs/pipelines/tutorials/dynamic-pipelines-and-annotations-using-bazel) | GitLab Dynamic Child Pipeline 等也能生成配置；差异是产品组合与调度模型，不是概念独占 | high for mechanism; medium for uniqueness | passed |
| BK-C08 | Hooks、Plugins 与 Templates 构成从 Agent 生命周期到中心模板的多层扩展面 | [Agent hooks](https://buildkite.com/docs/agent/hooks)；[Plugins](https://buildkite.com/docs/pipelines/integrations/plugins)；[Platform controls](https://buildkite.com/docs/pipelines/best-practices/platform-controls) | Hooks/Plugin 多在客户执行面运行；Pipeline Templates 为 Enterprise 功能，不能写成所有套餐默认能力 | high | passed |
| BK-C09 | Stacks API 允许自定义调度器把预定 Job 转换为 Kubernetes、VM、Serverless 或容器上的 Agent | [Stacks API](https://buildkite.com/docs/apis/agent-api/stacks) | 它扩展 Job provisioning，不替代客户对底层 Scheduler、容量与故障恢复的实现责任 | high | passed |
| BK-C10 | Hosted Agent 对每个 Job 创建临时执行环境，完成后销毁，Cache Volume 可保留 | [Hosted agents](https://buildkite.com/docs/agent/buildkite-hosted) | Linux 与 macOS 实现不同；区域、硬件、网络和费用限制仍需按当前套餐核验 | high | passed |
| BK-C11 | Test Engine 是跨 Build 的结构化测试数据层，也可接收非 Buildkite CI 的结果 | [Test Engine overview](https://buildkite.com/docs/pipelines/configure/tests) | Collector、测试身份、数据保留和套餐限制会影响可用性；不是所有测试框架都有深度 Collector | high | passed |
| BK-C12 | `bktec` 可依据历史耗时持续重平衡测试分片，并应用 mute/skip 状态 | [Test Engine Client](https://buildkite.com/docs/pipelines/speed-up-builds-with-bktec) | 历史分布不能保证未来等长；初始化、动态测试与共享状态可能降低分片效果 | high | passed |
| BK-C13 | Flaky Workflow 能检测、标记、通知和隔离测试，但不会修复根因 | [Reduce flaky tests](https://buildkite.com/docs/pipelines/reduce-flaky-tests)；[Workflows](https://buildkite.com/docs/test-engine/workflows) | mute/skip 可能降低门禁敏感度；必须保留 Owner、恢复条件和修复 SLA | high | passed |
| BK-C14 | Package Registries 可服务 Buildkite 与其他 CI/CD，并覆盖多种 Package/OCI/Module/模型生态 | [Package Registries](https://buildkite.com/docs/package-registries) | 格式支持不等于完整的签名、Promotion、Policy 和 Provenance 控制面 | high | passed |
| BK-C15 | Buildkite 的 Agentic CI 有两条路径：Agent 维护 Pipeline，以及 Agent 作为 Pipeline Step 运行 | [AI agents in Pipelines](https://buildkite.com/docs/platform/ai-agents) | 这是既有 CI 上的能力叠加，不等于底层编排模型由 Agent 取代 | high | passed |
| BK-C16 | Remote MCP 默认使用 OAuth；API Token 直连端点面向 Headless Agent 且明确为 Preview | [MCP docs](https://buildkite.com/docs/apis/mcp-server)；[Preview changelog](https://buildkite.com/resources/changelog/363-remote-mcp-server-api-access-token-support-is-now-available-preview/) | MCP 工具权限仍受 Token Scope 与组织权限限制；Preview 路径不宜成为关键发布单点 | high | passed |
| BK-C17 | 当前 Model Providers 文档仅列 Anthropic 模型支持 | [Model Providers](https://buildkite.com/docs/apis/model-providers) | 其他失败分析 Plugin 可调用 Bedrock/OpenAI，不能把 Plugin Provider 与原生 Model Providers 混写 | high | passed |
| BK-C18 | Reddit 2025 年公开材料证明 Buildkite 可承载完整移动 CI 迁移，并结合 Hosted Compute、Kubernetes、动态图与缓存 | [Reddit Engineering](https://www.reddit.com/r/RedditEng/comments/1megwf1/)；[Buildkite case](https://buildkite.com/resources/case-studies/reddit/) | 多项架构和流程同时变化，提速不能单独归因给 Buildkite；只确认至文章时间点 | medium-high | passed |
| BK-C19 | Canva 的案例证明 CI 性能由任务图、Build System、缓存、数据移动和 Agent 生命周期共同决定 | [Canva Engineering](https://www.canva.dev/blog/engineering/faster-ci-builds-at-canva/) | Canva 的主要增益混合 Bazel/RBE、大实例、Pipeline 生成器和 Agent Warm-up；不能作为 Buildkite 单因素案例 | high for architecture lesson; low for attribution | passed |
| BK-C20 | Rippling 的案例证明 Buildkite Control Plane 可扩展到大规模自管 AWS Fleet，也暴露 Spot/Queue 恢复需要客户自建逻辑 | [Rippling Engineering](https://www.rippling.com/blog/how-rippling-used-spot-instances-to-save-and-scale-ci-cd) | 2023 材料已旧；规模数字与成本结果不能外推，动态换 Queue 当时不是内建能力 | medium-high | passed |
| BK-C21 | Buildkite 的灵活性与客户执行面责任来自同一架构选择 | BK-C01—C10；官方 [Self-hosted agents](https://buildkite.com/docs/agent/self-hosted) 明确客户负责 provision、scale、security、maintenance | Hosted Agents 可减少责任，但会改变网络、硬件、成本与可定制范围 | high | passed |
| BK-C22 | Dagger 与 Buildkite 主要是相邻层：Dagger 编程任务执行语义，Buildkite 编程 Job Graph 和 Agent Fleet | [[50_deepdives/dagger/README|Dagger Deep Dive]]；BK-C05—C09 | 两者功能有扩张和重叠；公开材料未找到可署名的共同客户，技术可组合不等于客户事实 | medium-high | passed |
| BK-C23 | Buildkite 最适合异构计算、大型 Monorepo/测试矩阵、移动端或需要保留私网算力的平台团队 | BK-C02、C05、C09—C13、C18—C21 | 对少量静态 Job、无 Agent 运维能力或强依赖 SCM 原生一体化体验的团队，复杂度与成本可能更高 | medium-high | passed |

## 状态与版本审计

| 对象 | 截至 2026-08-02 的可核验状态 | 证据 | 不能写成 |
|---|---|---|---|
| Buildkite Pipelines / Dynamic Pipelines | 当前正式文档能力；页面未给统一 GA 标签 | [Pipelines](https://buildkite.com/docs/pipelines)、[Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) | Buildkite 独有或无限制动态图 |
| Buildkite Agent | 开源 MIT；Latest Stable `v3.135.0`（2026-07-29）；`v4.0.0-beta.9` 为 Pre-release | [Repository](https://github.com/buildkite/agent)、[Releases](https://github.com/buildkite/agent/releases) | v4 已 GA，或 SaaS Control Plane 开源/可完全退出 |
| Hosted Agents | 当前正式文档能力 | [Hosted agents](https://buildkite.com/docs/agent/buildkite-hosted) | 所有区域、OS、硬件和网络需求均支持 |
| Elastic CI Stack / Kubernetes Agent Stack | 当前正式文档和官方开源部署路径 | [AWS Stack](https://buildkite.com/docs/agent/self-hosted/aws/elastic-ci-stack)、[Kubernetes Stack](https://buildkite.com/docs/agent/self-hosted/agent-stack-k8s) | 零运维或 Buildkite 对客户云资源负责 |
| Test Engine / `bktec` | 当前产品文档能力；部分功能受 Pro/Enterprise 套餐限制 | [Test Engine](https://buildkite.com/docs/pipelines/configure/tests)、[`bktec`](https://buildkite.com/docs/pipelines/speed-up-builds-with-bktec) | 自动修复测试、保证等长分片 |
| Package Registries | 当前产品文档能力 | [Package Registries](https://buildkite.com/docs/package-registries) | 已包含任意制品治理、签名与 Promotion |
| Interactive Remote MCP | 当前官方默认 OAuth 路径；页面未标 GA/Preview | [MCP Server](https://buildkite.com/docs/apis/mcp-server) | 已明确 GA |
| Headless Remote MCP `/direct` | Preview，2026-06-12 Changelog 明示 | [Changelog](https://buildkite.com/resources/changelog/363-remote-mcp-server-api-access-token-support-is-now-available-preview/) | GA 后台 Agent 接口 |
| Model Providers | 当前文档仅支持 Anthropic | [Model Providers](https://buildkite.com/docs/apis/model-providers) | 原生多模型平台；Plugin 的其他 Provider 不等同于此能力 |

## 证据冲突与处理

### “无限并发”与真实吞吐

- Buildkite 产品材料强调没有平台并发限制。
- 实际吞吐仍受 Agent 数量、启动速度、Queue 分片、源码/依赖下载、Cache、外部服务和 Build Graph 限制。
- 正式结论写成“控制面不以固定并发席位限制扩展路径”，不写成“任务可以无限并行”或“增加 Agent 必然变快”。

### “客户掌控执行面”与“低运维”

- SaaS Control Plane 减少 Jenkins Controller 等平台运维。
- Self-hosted Agent Fleet 的镜像、容量、升级、Cache、Spot、网络与故障域仍由客户负责。
- Hosted Agents 降低这些责任，但减少硬件、网络与区域控制；两种模式必须分开计算 TCO。

### “Dynamic Pipeline 是差异化”与“其他平台也能动态生成”

- GitLab 官方支持由 Job 生成配置并触发 Dynamic Child Pipeline；Jenkins/GitHub/Harness 也有不同程度的代码化和矩阵能力。
- Buildkite 的差异不是发明动态图，而是把 `pipeline upload`、Queue 路由、开放 Agent、Agent Stack 与同一 Build 的可视化组合成主要工作模型。

### “Test Engine 管理 Flaky”与“测试已可靠”

- Detect、Label、Mute、Skip 和 Workflow 能降低 flaky 对主干的干扰并形成修复任务。
- 隔离只改变门禁影响，不改变根因；若没有 Owner、SLA 和恢复条件，会把可靠性债务隐藏起来。

## 仍未关闭的证据缺口

- Buildkite SaaS Control Plane 的完整可用性架构、跨区域灾难恢复和公开 SLA 细节；
- Hosted Agents 在全部区域、数据驻留、硬件 SKU、Cache 隔离和网络性能上的采购级矩阵；
- Dynamic Pipeline 在超大图、频繁上传和跨 Queue 下的独立规模基准；
- Test Engine 分片算法、冷启动数据需求和不同测试分布下的独立效果；
- Agentic CI 的正确率、误操作率、单位成功成本和生产写动作案例；
- Buildkite 与 GitHub Actions、GitLab、Harness 在相同负载上的独立 TCO/可靠性基准；
- 客户从 Buildkite Control Plane 退出时 Pipeline 历史、Test 数据与 Registry 元数据的完整迁移路径。
