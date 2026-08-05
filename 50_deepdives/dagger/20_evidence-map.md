---
title: Dagger Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - tool/dagger
status: complete
as_of: 2026-08-02
confidence: high
---

# Dagger Claim—Evidence—Gap Matrix

## 证据标记

- **A：** 当前官方文档、源码、版本化 Release，可证明机制、接口、默认值与状态。
- **B：** 官方 Changelog、工程博客，可证明发布轨迹与厂商架构意图。
- **C：** Dagger 发布的署名客户案例，只能证明该客户公开采用及厂商记录的结果。
- **分析：** 主 Agent 基于多个事实形成的架构或采用判断，不冒充来源原话。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| DAG-C01 | Dagger 是可编程软件交付执行与编排层，不只是 Pipeline-as-Code 语法 | [[00_sources/briefs/2026-dagger-engine-modules-execution|Engine、Module 与增量执行]]；[官方概览](https://docs.dagger.io/) 把 Execution Engine、System API、SDK、Modules 与 CLI 置于同一平台 | “CI Orchestration Engine”不是完整企业 CI 控制面的同义词 | high | passed |
| DAG-C02 | Function 调用由 Engine 形成类型化、惰性求值的 DAG，可按依赖并发 | [[00_sources/briefs/2026-dagger-engine-modules-execution|Engine、Module 与增量执行]]；[API Internals](https://docs.dagger.io/reference/api/internals/) | 代码看似顺序不等于严格串行；实际并发受 CPU、内存、CNI、Registry 与外部系统限制 | high | passed |
| DAG-C03 | Dagger 的可移植单位是 Module/Function 执行逻辑，不是全部运行环境 | 官方 [GitHub Actions](https://docs.dagger.io/getting-started/ci-integrations/github-actions/) 与 [Container Runtimes](https://docs.dagger.io/reference/container-runtimes/)；[[50_deepdives/dagger/research-integrations-governance-2026-07-28|治理研究]] | 身份、网络、架构、Cache、Secret Provider、审批与 CI 事件语义仍随环境变化 | high | passed |
| DAG-C04 | 成熟采用路径是现有 CI 触发与治理、Dagger 执行 | GitHub、GitLab、Jenkins 官方集成链路；[[50_deepdives/dagger/research-integrations-governance-2026-07-28|治理研究]] | Cloud Checks 正向托管 CI 控制面扩张，因此不能永远把 Dagger 限定为“只在 CI Job 内运行” | high | passed |
| DAG-C05 | Cloud Checks 与 Cloud Engines 扩大了 Dagger 的控制面，但截至 2026-08-02 仍按明确状态证据标记为 Early Access | [[00_sources/briefs/2026-dagger-cloud-engines-checks|Cloud Engines 与 Cloud Checks]]；[Dagger Changelog](https://dagger.io/changelog/) 明确标记 Early Access | Cloud 配置文档使用正式能力语气但不显示状态；应以明确 Changelog 标签约束成熟度 | high | passed |
| DAG-C06 | 内容寻址、Layer/Volume/Function Cache 和增量执行是 Dagger 的核心差异化 | [[00_sources/briefs/2026-dagger-engine-modules-execution|Engine、Module 与增量执行]]；[Built-In Caching](https://docs.dagger.io/features/caching/) | Cold Cache、Cache 传输、GC、输入过宽或外部 I/O 可能抵消收益；没有行业统一加速比例 | high for mechanism; medium for outcome | passed |
| DAG-C07 | Function Cache 改变执行语义，带副作用或外部可变状态的 Function 必须显式设置策略 | [Function Caching](https://docs.dagger.io/extending/function-caching/) 说明默认 TTL、Session 与 `never`；[[00_sources/briefs/2026-dagger-engine-modules-execution|Source Brief]] | `cache="never"` 只禁止 Function 结果复用，不自动关闭函数内部 Layer Cache | high | passed |
| DAG-C08 | Function 默认不能隐式访问宿主资源，类型化参数构成能力输入边界 | [[00_sources/briefs/2026-dagger-sandbox-secrets-engine-security|Sandbox、Secret 与 Engine 边界]]；[Sandboxed Runtime](https://docs.dagger.io/features/sandbox/) | 顶层 Module 可把显式资源继续传给依赖；传入大目录、Socket 或高权 Secret 后能力边界随之扩大 | high | passed |
| DAG-C09 | Function 沙箱不能证明 Engine 已被强化；自管 Engine 当前仍需特权且默认开放 `insecureRootCapabilities` | [[00_sources/briefs/2026-dagger-sandbox-secrets-engine-security|Sandbox、Secret 与 Engine 边界]]；[Engine Configuration](https://docs.dagger.io/reference/configuration/engine/) | 该默认值可关闭，但兼容性与实际模块需求必须验证；Cloud Engine 隔离细节未公开 | high | passed |
| DAG-C10 | Dagger Secret 降低日志与 Cache 泄漏风险，但不是 IAM、DLP 或恶意代码隔离 | [[00_sources/briefs/2026-dagger-sandbox-secrets-engine-security|Sandbox、Secret 与 Engine 边界]]；[Secrets](https://docs.dagger.io/extending/secrets/) | 明文被用户写入普通 File 后可能进入 Cache；获准网络、制品输出或 Socket 仍可成为外泄通道 | high | passed |
| DAG-C11 | Dagger Trace 是可观察证据，不是不可篡改的合规审计或发布证明 | [[00_sources/briefs/2026-dagger-cloud-engines-checks|Cloud Engines 与 Cloud Checks]]；[Dagger Cloud](https://docs.dagger.io/reference/configuration/cloud/) | Public Repo 的 CI Trace 可能默认公开；Trace 仍需与 Commit、Artifact Digest、Policy 和目标系统审计关联 | high | passed |
| DAG-C12 | Module 提供跨语言共享 API，同时形成新的版本与供应链治理面 | [Reusability](https://docs.dagger.io/features/reusability/) 说明 Git 来源、版本 Pin 与跨语言调用；[[00_sources/briefs/2026-dagger-engine-modules-execution|Source Brief]] | Daggerverse 是公开索引，不等于企业准入目录；八种 SDK 的支持等级和功能对等未统一证明 | high for mechanism; medium for maturity | passed |
| DAG-C13 | LLM Primitive 复用 Function、Env、Container 和 Trace，并能双向连接 MCP | [[00_sources/briefs/2026-dagger-llm-mcp-runtime|LLM、Tool Use 与 MCP]]；[LLM Integration](https://docs.dagger.io/features/llm/) | 2025 发布与 Next API 均显示 Experimental/未稳定；Tool 可见不等于动作授权 | high for mechanism; medium for production maturity | passed |
| DAG-C14 | Dagger Agent 的确定性执行环境不能证明模型正确性或自治收益 | [[00_sources/briefs/2026-dagger-llm-mcp-runtime|LLM、Tool Use 与 MCP]]；官方 [Evals as Code](https://dagger.io/blog/evals-as-code/) 记录模型和工具失败 | 缺少独立正确率、越权率、MTTR 与单位成功成本基准 | high for boundary; low for outcome | passed |
| DAG-C15 | Airbyte 的提速降本是 Dagger、Cloud Cache、Runner 架构与逻辑重构的联合结果 | [[00_sources/briefs/2023-dagger-airbyte-ci-case-study|Airbyte Case]] | 厂商发布的客户自述，没有独立实验设计或单因素归因 | medium | passed |
| DAG-C16 | Engine 特权需求可能改变受监管平台的部署拓扑与组织成本 | [[00_sources/briefs/2025-dagger-puzzle-kubevirt-ci|Puzzle KubeVirt Case]] | 单客户方案，增加 VM、持久卷和路由复杂度；不等于所有企业都必须使用 VM | medium-high | passed |
| DAG-C17 | Dagger 适合复用半径大、容器化程度高、需要本地复现或多 CI 的平台工程场景 | DAG-C01—C07；[[50_deepdives/dagger/research-adoption-alternatives-ai-2026-07-28|采用与替代研究]] | 对简单单仓、少量稳定原生 CI Step 或无法容器化的工作负载，引入 Engine/SDK/Module 的 TCO 可能更高 | medium-high | passed |
| DAG-C18 | Dagger 不替代 Artifact Provenance、Signature、Policy、SLO 或人工 Approval | Dagger 公开机制只证明执行与 Trace；[[00_sources/briefs/2026-github-artifact-attestations-slsa|Artifact Attestations 与 SLSA]] 说明可信构建与下游验证要求 | Dagger 可以调用生成/验证这些证据的工具，但工具调用成功不能由此升级为独立证明 | high | passed |

## 状态与版本审计

| 对象 | 截至 2026-08-02 的可核验状态 | 证据 | 不能写成 |
|---|---|---|---|
| Dagger CLI / Engine | GitHub Latest `v0.21.8`，2026-07-29；Apache-2.0 源码 | [Release](https://github.com/dagger/dagger/releases/tag/v0.21.8)、[Repository](https://github.com/dagger/dagger) | 所有 0.21.x 接口都冻结或有长期兼容 SLA |
| 官方稳定文档 | 导航主要显示 `0.21.4`；比 Latest Release 落后三个 Patch | [Installation](https://docs.dagger.io/getting-started/installation/)、Release | 文档版本与 Latest 完全同步 |
| 八种 SDK | 官网称 8 种；仓库可见 .NET、Elixir、Go、Java、PHP、Python、Rust、TypeScript | [Repository](https://github.com/dagger/dagger/tree/main/sdk) | 八种 SDK 均同等 GA、功能完全对等 |
| Dagger Cloud Trace / Module | 当前文档可用；页面未提供统一 GA 标签 | [Cloud Configuration](https://docs.dagger.io/reference/configuration/cloud/) | 已有不可篡改审计、完整数据驻留和企业 SLA 证明 |
| Cloud Engines | Early Access | [Changelog](https://dagger.io/changelog/) | GA 托管计算 |
| Cloud Checks | Early Access；当前面向 GitHub Commit Check 路径 | [[00_sources/briefs/2026-dagger-cloud-engines-checks|Source Brief]] | 已等价替代所有 Jenkins/GitHub/GitLab/CD 能力 |
| LLM / Env / MCP Agent | 功能已公开；成熟度按 Experimental / 未稳定处理 | [[00_sources/briefs/2026-dagger-llm-mcp-runtime|Source Brief]] | 稳定生产自治平台或已证明业务收益 |
| Modules v2 / Workspace Next | In development / Unreleased | [Changelog](https://dagger.io/changelog/)、[Next Docs](https://docs.dagger.io/next/) | 当前稳定合同 |

## 证据冲突与处理

### “Dagger 不是 CI 平台”与“Cloud Checks 是 CI 平台”

- **旧结论成立范围：** 开源 Engine 和 Hybrid Integration 不处理完整 Git Event、Runner 管理和平台审批。
- **新增事实：** Cloud Checks 明确进入 Git Event、托管 Engine 和 Commit Status。
- **处理：** 正式报告写成“双路径产品”：成熟的嵌入式执行层 + Early Access 托管 CI 扩张，不再使用绝对句。

### “Fully sandboxed”与“Privileged Engine”

- **前者：** Function 默认看不到宿主资源，资源须经类型化参数传入。
- **后者：** 承载 Function 的 Engine 自身仍需特权，且默认开放不安全 Root Capability。
- **处理：** 安全图必须分 Function、Engine、Runner/Host 三层，禁止用单一“沙箱”标签概括。

### “稳定功能页未标 Experimental”与“LLM 未稳定”

- **稳定功能页：** 展示 LLM、Env、Tool Use 与 MCP 的使用方式。
- **历史发布/Next API：** 分别标记 Experimental 和 Not Yet Stabilized。
- **处理：** 能力存在性为高置信，生产成熟度为中低置信；进入正式页面必须标记未稳定。

## 仍未关闭的证据缺口

- Dagger Cloud Engines 的多租户隔离、区域、数据驻留、Cache/Trace 保留和 SLA；
- Cloud Checks 的完整 Git Provider、事件、审批、环境、部署与灾难恢复能力；
- 八种 SDK 的统一支持等级、兼容矩阵和弃用政策；
- 共享自管 Engine 的跨 Repo/Tenant Cache、网络、文件系统与资源隔离保证；
- 独立的 Dagger 与原生 CI、Bazel/Nix、Earthly 或 Tekton 对照基准；
- LLM Agent 的正确率、越权率、成本、回归和生产写动作数据；
- Cloud 数据导出与退出迁移机制。
