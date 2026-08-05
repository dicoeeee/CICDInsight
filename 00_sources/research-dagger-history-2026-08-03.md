---
title: "Dagger 发展史：从 CUE/BuildKit 原型到函数化交付与 Cloud/Agent 平台（2026-08-03）"
tags:
  - research/cicd
  - company/dagger
  - history
status: complete
as_of: 2026-08-03
confidence: high
---

# Dagger 发展史：从 CUE/BuildKit 原型到函数化交付与 Cloud/Agent 平台（2026-08-03）

> [!info] 研究范围与证据规则
>
> 本文仅使用 Dagger 官方博客、Changelog、文档、官方 GitHub 仓库/Release 与官方公告；为核对 Solomon Hykes 的 Docker 经历，补充使用 Docker 官方博客。未以 Wikipedia、媒体稿或搜索摘要作为关键证据。除另注外，所有链接均于 **2026-08-03** 访问。页面无发布日期时明确标为“页面未标日期”；“首次”只在官方原文明确发布或历史回顾时使用。本文区分来源明确事实、分析推断和证据缺口。

## 写作提纲

1. 核对可公开定年的原型、公开发布、创始团队和问题背景；
2. 还原 Engine、BuildKit、GraphQL/API 与 SDK 的早期形态和转向；
3. 建立 Functions、Modules、typed API 的演进时间轴；
4. 逐项核对 Cloud、AI/MCP 的发布时间与产品状态；
5. 用官方 GitHub Release 锁定截至日版本，并保留未能核验的边界。

## 结论先行

**来源明确事实。** Dagger 的首个公开原型在 2021 年是 **CUE 到 BuildKit 的前端**；2022-03-30 以公开 Beta 形式推出，官方将它定位为由 BuildKit 驱动、可在不同环境运行的 CI/CD devkit。Solomon Hykes、Sam Alba、Andrea Luzzardi 是可由 Dagger 官方公告逐名确认的三位共同创始人；Dagger 官方还称其由 Docker 的创造者推出，Docker 官方材料确认 Hykes 是 Docker 项目创建者。2022 年下半年，产品从 CUE 为唯一编程面转向由 Dagger Engine 执行、GraphQL 统一调用的 Go/Python/Node.js SDK；2024-02 的 Dagger 0.10 将用户代码变成可复用的 Functions/Modules，并以类型化跨语言 API、CLI introspection 和 Daggerverse 扩展为函数生态。Dagger Cloud 于 2023-09 以 Early Access 发布，2024-04 发布 beta Traces；2026-03 发布的 Cloud Engines 仍为 Early Access，Cloud Checks 在当前 Changelog 亦明确为 Early Access。LLM Primitive 于 2025-04 以 Experimental 形态公布，2025-10 的 0.19 进一步加入面向 agent 的环境、workspace 和 MCP-server API。官方 GitHub Release API 显示，截至 2026-08-03 最新稳定 Release 是 **v0.21.8（2026-07-29）**，非 prerelease。

**分析性归纳（非厂商事实）。** Dagger 的主线不是“把 YAML 换成任意一种 SDK”，而是先以 BuildKit/CUE 验证容器 DAG 的可编程性，再用 GraphQL 把 Engine 变成多语言的稳定中间层，最后把交付逻辑提升为可组合、可分发的 typed Functions/Modules。Cloud 与 Agent 能力在此基础上增加协作、托管计算和工具调用；它们不自动构成通用 CI 平台成熟度、生产授权或结果正确性的证明。

## 时间轴与逐项核验

| 时间 | 已核验事件 | 能证明什么 | 来源发布日期 / 访问日期 | 状态与证据边界 |
|---|---|---|---|---|
| 2021 | Dagger 官方在结束 CUE SDK 支持的回顾中称：首个公开原型是“essentially a CUE frontend to BuildKit”。 | 最早可公开定年的技术起点是 CUE + BuildKit，而非当前的 Functions/Modules/GraphQL 形态。 | [CUE SDK 回顾](https://dagger.io/blog/ending-cue-support/)：2023-12-18；访问 2026-08-03。 | 回顾文没有给出 2021 原型的具体月日、仓库首 commit 或公司设立日。 |
| 2022-03-30 | Dagger 官方公开宣布项目并称由 Docker 的创造者推出；其产品是由 BuildKit 驱动、可在任意环境运行的 portable CI/CD devkit。同期融资公告称当天推出 public beta。 | 可核验的公开发布节点、早期 BuildKit 基础和 public-beta 状态。 | [公开发布](https://dagger.io/blog/public-launch-announcement/)；[融资/公开 Beta 公告](https://dagger.io/blog/series-a/)：均为 2022-03-30；访问 2026-08-03。 | “公开 Beta”不是 GA；公告不能证明公司法律创立日期或 2021 前的私有研发起点。 |
| 2022-03-30 | Hykes 在官方融资公告中将 Sam Alba、Andrea Luzzardi 与自己称为“my co-founders”；Dagger 2024 官方回顾也称三人共同迭代出 Dagger 的想法。Docker 官方材料称 Hykes 是 Docker 项目创建者。 | 可确认共同创始团队为 Solomon Hykes、Sam Alba、Andrea Luzzardi；可确认 Hykes 与 Docker 的公开项目渊源。 | [Dagger 融资公告](https://dagger.io/blog/series-a/)：2022-03-30；[Dagger 官方回顾](https://dagger.io/blog/civo-navigate-north-america-recap/)：2024-02-26；[Docker 作者页](https://www.docker.com/contributors/solomon-hykes/)：页面未标日期；访问 2026-08-03。 | 未找到 Dagger 官方 About/公告可核验三人的完整入职日、法定职位变更、股权或公司注册日期；不据此推断现任管理团队。 |
| 2022-10-25—11-17 | 官方博客依次发布 Go SDK、Python SDK、Node.js SDK 与 GraphQL API。GraphQL 公告说明 SDK 不直接执行 Pipeline，而是把定义交给 Engine；GraphQL 是面向任何标准 client 的统一接口，CLI 也可直接调用。 | 早期形态从 CUE-only 转为“Engine + GraphQL + 多语言 SDK + CLI”；GraphQL 是 API 中间层，不是另一套执行器。 | [Go SDK](https://dagger.io/blog/go-sdk/)：2022-10-25；[Python SDK](https://dagger.io/blog/python-sdk/)：2022-11-08；[Node.js SDK](https://dagger.io/blog/nodejs-sdk/)：2022-11-16；[GraphQL API](https://dagger.io/blog/graphql/)：2022-11-17；访问 2026-08-03。 | 官方 GraphQL 公告列 Go/Python/JavaScript/TypeScript 的近期 SDK 推进；不能由此反推其他后来 SDK 的同等成熟度。 |
| 2023-09-26 | Dagger Cloud 作为首个商业产品进入 Early Access；当时是补充去中心化 Engine 的集中控制面，提供 Pipeline Visualization、Operational Insights 与 Distributed Caching，且明确是 BYO compute、不是托管 Engine。 | 初代 Cloud 的边界是可观测性/缓存控制面，而非替代 CI/运行 Engine。 | [Dagger Cloud Early Access](https://dagger.io/blog/dagger-cloud/)：2023-09-26；访问 2026-08-03。 | 该公告只证明当时 Early Access；不能将该状态直接延续为当前 Dagger Cloud 的整体状态。 |
| 2023-12-18 | Dagger 宣布停止继续支持 CUE SDK：CUE pipeline 仍可在 Engine 0.2 运行，但不再计划支持到较新 Engine；原因是社区对多语言 SDK 的需求及其采用增长。 | CUE 原型/早期 API 路线被正式收束，GraphQL-powered Engine + Go/Python/Node.js SDK 成为主线。 | [结束 CUE SDK 支持](https://dagger.io/blog/ending-cue-support/)：2023-12-18；访问 2026-08-03。 | 这不是 CUE 语言本身的技术评价，也不证明旧 CUE 项目已立即不可运行。 |
| 2024-02-28 | Dagger 0.10 发布 Dagger Functions：用户代码可由 Engine 编译成容器并暴露为 custom GraphQL API；Functions 可打包成 Modules，CLI 可 introspect 模块 API 并作为子命令调用。 | Functions/Modules/CLI 形成当前“typed、可组合、可复用交付 API”的公开转折点。 | [Introducing Dagger Functions](https://dagger.io/blog/introducing-dagger-functions/)：2024-02-28；访问 2026-08-03。 | “类型安全”指 SDK 生成 native binding 的开发体验；它不保证函数业务语义、外部副作用或部署结果正确。 |
| 2024-03-12 | Daggerverse 发布为 public Dagger Functions 的可搜索索引；官方说明模块以 Git 源码为基础、依赖默认 pinned、无中心 registry。 | Functions/Modules 从单仓库复用扩展为以 Git 身份/版本为基础的公共发现层。 | [Introducing the Daggerverse](https://dagger.io/blog/introducing-the-daggerverse/)：2024-03-12；访问 2026-08-03。 | Daggerverse 的“可搜索”不等于模块安全、维护质量或企业批准。 |
| 2024-04-16 | Dagger Cloud 发布 Traces beta，要求 Engine 0.11+，用 OpenTelemetry 展示 Function 调用、上下游操作、耗时与 GitHub Check 状态。 | Traces 是明确 beta 发布的函数/执行图可观测性能力。 | [Introducing Dagger Traces](https://dagger.io/blog/introducing-dagger-traces/)：2024-04-16；访问 2026-08-03。 | 本轮未找到将 Traces 明确宣布为 GA 的一手材料；当前文档/产品存在不应倒推 GA。 |
| 2025-04-23 | Dagger 发布 LLM Primitive，称其把 LLM 接入软件交付 workflow；初始公告明确标为 Experimental。 | LLM 是 Dagger 官方产品能力，但首次状态为 Experimental。 | [LLM Primitive](https://dagger.io/blog/llm/)：2025-04-23；访问 2026-08-03。 | Experimental 不能被写成生产稳定或自动授权；模型、工具和 Secret 的权限仍取决于显式传入的环境与外部系统控制。 |
| 2025-10-01 | Dagger 0.19 发布 “build-an-agent” 能力，包括 `Env.withWorkspace`、`Env.withModule`/`withCurrentModule` 与 `LLM.withMCPServer`；官方说明 MCP tools 继承 workspace 并把修改传给后续工具调用。 | Dagger 从 LLM primitive 扩展到带 workspace 状态、模块工具和外部 MCP server 的 agent 组装面。 | [Dagger 0.19](https://dagger.io/blog/dagger-0-19/)：2025-10-01；访问 2026-08-03。 | 这是工具调用和环境组合能力，不等于 agent 已取得合并、发布、部署或审批权。 |
| 2026-03-19 | Dagger 0.20.2 Changelog 发布 Cloud Engines：托管、自动扩缩、分布式缓存，`dagger --cloud` 可将执行放到云端；公告明确为 Early Access。 | Cloud 从 BYO compute 发展到可托管执行/缓存，但仍非 GA。 | [Dagger Changelog](https://dagger.io/changelog/)：v0.20.2，2026-03-19；访问 2026-08-03。 | 当前 Changelog 的 Early Access 是明确状态；不把“Dagger 自己用于生产”外推为外部客户 SLA、隔离或通用成熟度。 |
| 2026-05（精确日未标） | 当前 Changelog 描述 Cloud Checks：连接 Git provider、每次变更触发 `dagger check`、在 Cloud Engines 上自动扩缩，并明确标注 **In early access**。当前 Cloud 文档具体说明 GitHub App 检测 commit、加载根模块、发现 check Functions、在托管基础设施执行并回写 GitHub commit status。 | Cloud Checks 已形成“Git 触发 + 模块发现 + 托管 Engine + 状态回写”的官方产品路径；截至日仍为 Early Access。 | [Changelog](https://dagger.io/changelog/)：页面归在 2026-05 Cloud 条目，精确首次发布时间未标；[Cloud 配置文档](https://docs.dagger.io/reference/configuration/cloud/)：页面未标日期；访问 2026-08-03。 | Changelog 对 Cloud Checks 的条目还显示“Next release ~June 2026”，无法单独证明最初开放日；故只记录月份/当前 EA，不虚构精确发布日期或 GA。文档的“fully managed”描述不覆盖 SLA、区域、数据驻留、退出与审批治理。 |
| 2026-07-29 | Dagger 官方 GitHub Releases API 的 `releases/latest` 返回 `v0.21.8`，`prerelease: false`，`published_at: 2026-07-29T17:07:24Z`。 | 截至 2026-08-03 最新稳定 Release 是 v0.21.8。 | [v0.21.8 Release](https://github.com/dagger/dagger/releases/tag/v0.21.8)：2026-07-29；[GitHub Releases API](https://api.github.com/repos/dagger/dagger/releases/latest)：2026-08-03 查询。 | “latest Release”不代表每个 SDK package、Cloud 功能或 Next 文档都与该版本同日 GA。 |

## 技术演进：从 BuildKit 前端到函数化交付 API

### 1. 原型与 Engine：BuildKit 是早期执行底座，不是 Dagger 的全部抽象

**来源事实。** 2021 原型是 CUE frontend to BuildKit；2022 公告明确称 Dagger 由 BuildKit 驱动。到 2022-11，官方解释为所有 SDK/Pipeline 的实际运行都通过 Dagger Engine，客户端将定义或查询交给 Engine，Engine 返回与语言无关的结果。GraphQL 被选择为组合 Engine API 的统一接口，CLI 可调用 API；2024 Functions 发布再将 Engine 的核心操作和自定义函数统一到 Function 模型。[CUE 回顾](https://dagger.io/blog/ending-cue-support/) [公开发布](https://dagger.io/blog/public-launch-announcement/) [GraphQL API](https://dagger.io/blog/graphql/) [Functions](https://dagger.io/blog/introducing-dagger-functions/)

**分析推断。** BuildKit 是第一阶段的求值/缓存底座；Dagger 的差异化演进在于用 GraphQL 和类型化 SDK 将容器、文件、服务、Secret 等操作提升为可跨语言组合的交付 API，而非把 BuildKit 当作用户直接面对的 pipeline DSL。该判断不表示当前 Engine 的全部内部实现仍等同于早期 BuildKit：2026 Changelog 已写明正在以 native engine replacement 取代 BuildKit solver，属于持续中的架构变化，不能将其描述为已完成的全面替换。[Changelog](https://dagger.io/changelog/)

### 2. SDK/API：CUE-only → GraphQL 中间层 → 多语言原生 binding

| 阶段 | 来源明确事实 | 可用口径与边界 |
|---|---|---|
| 2021—早 2022 | CUE 是第一个公开原型和 Engine 发布时唯一的编程方式。 | 可以称“CUE-first”；不能称 CUE 是今天的主 SDK。 |
| 2022-10—11 | Go、Python、Node.js SDK 后，GraphQL API 公开；官方称 standard GraphQL client 可使用 Engine，而官方 SDK 自动生成 binding 以隐藏手写 query。 | 可以称“GraphQL 是跨语言执行中间层”；不能说 GraphQL client 与官方 SDK 在开发体验上等价。 |
| 当前仓库说明 | `dagger/dagger` 官方 README 将 SDK 描述为 8 种语言（Go、Python、TypeScript、PHP、Java、.NET、Elixir、Rust），由 API schema 生成，提供类型安全和编辑器支持。 | 这是截至访问日的仓库自述；不以此推断八种 SDK 的 API 覆盖、生命周期或稳定性完全相同。 |

### 3. Functions、Modules 与 typed API：从“定义 Pipeline”到“分发并组合能力”

**来源事实。** Dagger 0.10 把用户函数放进 Module；Engine 在运行时将 Function 编译为容器并暴露 custom GraphQL API。Function 可跨语言调用，SDK 为依赖生成 native code binding；CLI 通过 introspection 把函数/参数映射为子命令/flags。Modules 以 Git source 为真源、版本依赖默认 pinned，Daggerverse 后续提供发现索引。当前官方仓库又将 `Container`、`Directory` 等对象描述为 typed artifacts，可跨 SDK 与 module 边界传递，无须序列化。[Functions](https://dagger.io/blog/introducing-dagger-functions/) [Daggerverse](https://dagger.io/blog/introducing-the-daggerverse/) [官方仓库 README](https://github.com/dagger/dagger)

**分析推断。** 这一演进把可复用单位从“某个 CI 配置文件/步骤模板”转换为“有输入输出类型、可被其他函数组合的交付 API”。但类型和封装不能替代输入验证、测试、签名、外部策略或人工审批；带副作用的 Function 尤其不能因为可组合而被视为幂等或已授权。

## Cloud、Traces、Checks 与 Engines：发布时间和状态矩阵

| 对象 | 最早本轮可核验公开节点 | 截至 2026-08-03 能确认的状态 | 不可外推的事项 |
|---|---|---|---|
| Dagger Cloud | 2023-09-26 Early Access：集中控制面、可视化、运营洞察、分布式缓存，BYO compute。 | 当前文档存在 Cloud 配置、Trace 与 Checks 能力；本轮未发现一条覆盖整个 Cloud 的统一 GA 公告。 | 不能将 2023 EA 或任一当前文档写成整个 Cloud 已 GA。 |
| Cloud Traces | 2024-04-16，Dagger Cloud 的 **beta** view；需 Engine 0.11+。 | 当前产品继续使用 Trace 概念；没有找到明确 GA 公告。 | 不写 GA；也不把 Trace 可见性等同于完整审计、保留期或导出承诺。 |
| Cloud Engines | 2026-03-19，v0.20.2。 | **Early Access**；托管 Engine、自动扩缩、分布式缓存，`dagger --cloud`。 | 不写 SLA、客户隔离、区域、价格、长期接口稳定性或成熟采购结论。 |
| Cloud Checks | Changelog 的 2026-05 条目，精确首次日未标。 | **Early Access**；GitHub App 监听提交、发现 check Functions、托管执行、回写 status。 | 当前文档没有替代 Changelog 的 GA 声明；不能写已支持多 Git provider 或企业审批/发布治理。 |

## LLM、MCP 与 AI：能力与控制边界

**来源明确事实。** 2025-04-23 的 LLM Primitive 首次公告标为 Experimental。2025-10 的 0.19 把 agent 构建组件写进产品发布：`Env` 可携带 workspace，Module 可进入 agent environment，`LLM.withMCPServer` 可把作为服务运行的 MCP server 加入 LLM，工具继承 workspace 并可传递后续修改。官方 2025-08 的 Evals as Code 文章还说明团队将 Dagger API 转为模型可调用的 tools，并以 evals 测试该实现。[LLM Primitive](https://dagger.io/blog/llm/) [Dagger 0.19](https://dagger.io/blog/dagger-0-19/) [Evals as Code](https://dagger.io/blog/evals-as-code/)

**截至日口径。** 可以说 Dagger 已提供把 LLM/MCP tool use 放进 Engine/Module/Workspace 环境的产品化路径；不得说 LLM capability 已 GA，因为初始公告是 Experimental，且本轮未找到覆盖整个 LLM/MCP 面的明确 GA 宣告。当前 `next` 文档仍把部分 LLM API 标为未稳定，故不应把 Next API 当作已发布合同。[LLM Integration](https://docs.dagger.io/features/llm/) [Next Query API](https://docs.dagger.io/next/extending/types/query/)

**控制边界。** MCP 可调用 Function 或连接外部 server，只描述可达的工具面；是否能读代码、使用 Secret、修改文件、合并、发布或访问云资源，仍由传入的对象/Secret、模块实现、CI token、云 IAM、外部 policy 和人工审批分别决定。Function 或 agent 成功返回也不能替代测试、扫描、签名、SLO 或人工 Oracle。

## 当前版本与状态快照

| 项目 | 截至 2026-08-03 的可核验状态 | 证据边界 |
|---|---|---|
| Engine/CLI 最新 GitHub Release | **v0.21.8**，2026-07-29 发布，非 prerelease。 | 为官方 GitHub Repo 的 latest Release；不等价于每个语言包或 Cloud 面的版本/GA。 |
| SDK | 官方 README 说明为 8 种语言、schema-generated、typed。 | README 是当前自述，未逐一核对每种 SDK 的独立 release 和兼容矩阵。 |
| Functions/Modules | 0.10 已公开发布；当前是产品核心抽象。 | 不能由“核心抽象”推断所有 module 质量或生态安全。 |
| Dagger Cloud/Traces | Cloud 2023 EA；Traces 2024 beta；未找到整个 Cloud 或 Traces 的明确 GA 公告。 | 保持 `status: explicit-GA-not-found`。 |
| Cloud Engines/Checks | 两者截至日均有 Changelog 的 **Early Access** 明示。 | 不作为成熟通用 CI 平台能力承诺。 |
| LLM/MCP | LLM Primitive 初始为 Experimental；0.19 已发布 agent/MCP workspace 组件。 | 不写 GA；Next 文档不作为 released API。 |

## 证据缺口与不应外推的结论

1. **成立日期缺口。** 找到的是 2021 公开原型和 2022 public beta，不是 Dagger 公司法定成立日；不能把任一日期写成“公司创立日”。
2. **创始团队职责/现任缺口。** 三位共同创始人可核验，但本轮没有官方一手材料同时给出其完整创办日期、股份或当前所有管理职位。
3. **BuildKit 替换边界。** 当前 Changelog 说正以 native engine 替换 BuildKit solver；没有足够材料证明在 2026-08-03 已完成全部替换，故不能写成完成事实。
4. **Cloud/Traces GA 缺口。** Cloud 首发为 EA、Traces 首发为 beta；未找到各自 GA 公告。只有 Cloud Engines 与 Cloud Checks 有截至日的明确 EA 标签。
5. **Cloud Checks 首发日缺口。** 当前 Changelog 的条目只有 2026-05 归档上下文及“Next release ~June 2026”，没有可核验的首次开放精确日期；须保持月份级/`date-not-published`。
6. **AI 稳定性与授权缺口。** 初始 LLM Primitive 为 Experimental，部分 Next API 未稳定；没有一手材料证明模型正确率、安全性、企业授权或自动发布结果。
7. **版本范围边界。** v0.21.8 是 GitHub latest Release 的实时 API 结果；它不能替代 SDK 独立发布、Cloud rollout 或文档版本的逐项核验。

## 来源清单

| 来源 | 类型 | 发布日期 | 访问日期 | 用途 |
|---|---|---:|---:|---|
| [Dagger 公开发布](https://dagger.io/blog/public-launch-announcement/) | Dagger 官方博客/公告 | 2022-03-30 | 2026-08-03 | public launch、BuildKit、早期定位。 |
| [Dagger Series A / public beta](https://dagger.io/blog/series-a/) | Dagger 官方公告 | 2022-03-30 | 2026-08-03 | 三位共同创始人、public beta、融资背景。 |
| [结束 CUE SDK 支持](https://dagger.io/blog/ending-cue-support/) | Dagger 官方博客 | 2023-12-18 | 2026-08-03 | 2021 CUE/BuildKit 原型与多语言转向。 |
| [Go SDK](https://dagger.io/blog/go-sdk/)、[Python SDK](https://dagger.io/blog/python-sdk/)、[Node.js SDK](https://dagger.io/blog/nodejs-sdk/)、[GraphQL API](https://dagger.io/blog/graphql/) | Dagger 官方博客 | 2022-10-25 / 11-08 / 11-16 / 11-17 | 2026-08-03 | SDK/API 时间轴与 Engine 调用模型。 |
| [Dagger Functions](https://dagger.io/blog/introducing-dagger-functions/) | Dagger 官方博客 | 2024-02-28 | 2026-08-03 | Functions、Modules、CLI、GraphQL/typed composition。 |
| [Daggerverse](https://dagger.io/blog/introducing-the-daggerverse/) | Dagger 官方博客 | 2024-03-12 | 2026-08-03 | Modules 分发与发现模式。 |
| [Dagger Cloud Early Access](https://dagger.io/blog/dagger-cloud/) | Dagger 官方博客 | 2023-09-26 | 2026-08-03 | 初代 Cloud 能力与 BYO compute 边界。 |
| [Dagger Traces](https://dagger.io/blog/introducing-dagger-traces/) | Dagger 官方博客 | 2024-04-16 | 2026-08-03 | Traces beta 与 Engine 0.11 前置条件。 |
| [Dagger Changelog](https://dagger.io/changelog/) | Dagger 官方 Changelog | 条目见 2026-03 / 2026-05 | 2026-08-03 | Cloud Engines/Checks EA、native engine roadmap。 |
| [Dagger Cloud 配置](https://docs.dagger.io/reference/configuration/cloud/) | Dagger 官方文档 | 页面未标日期 | 2026-08-03 | Checks 的 GitHub App、发现、托管执行与 status 回写流程。 |
| [LLM Primitive](https://dagger.io/blog/llm/)、[Dagger 0.19](https://dagger.io/blog/dagger-0-19/)、[Evals as Code](https://dagger.io/blog/evals-as-code/) | Dagger 官方博客 | 2025-04-23 / 2025-10-01 / 2025-08-04 | 2026-08-03 | LLM Experimental、MCP/agent API、eval 路线。 |
| [官方仓库 README](https://github.com/dagger/dagger)、[v0.21.8 Release](https://github.com/dagger/dagger/releases/tag/v0.21.8)、[latest Release API](https://api.github.com/repos/dagger/dagger/releases/latest) | Dagger 官方 GitHub 仓库/API | README 未标日期；Release 2026-07-29 | 2026-08-03 | 当前 SDK/typed artifacts 自述与最新稳定版本。 |
| [Docker：Solomon Hykes 作者页](https://www.docker.com/contributors/solomon-hykes/) | Docker 官方材料 | 页面未标日期 | 2026-08-03 | Hykes 为 Docker 项目创建者的补充核验。 |

## 供主 Agent 整合（150 字内）

2021 CUE+BuildKit 原型，2022 Engine+GraphQL+多语言 SDK，2024 Functions/Modules。Cloud Engines/Checks 仍 EA；LLM/MCP 起点为 Experimental。最新稳定版 v0.21.8（2026-07-29）。
