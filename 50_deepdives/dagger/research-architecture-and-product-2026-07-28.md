---
title: Dagger 核心架构与产品状态一手研究
tags:
  - research/deep-dive
  - tool/dagger
  - cicd/engine
status: complete
as_of: 2026-07-28
topic_id: dagger
confidence: high
source_scope: official-docs-official-repository-official-releases
---

# Dagger：核心架构与产品状态（截至 2026-07-28）

> [!warning] 主 Agent 状态复核覆盖
> 本研究底稿写作时未在 Cloud 配置页找到 Cloud Checks 的 GA/Preview 标签。主 Agent 随后在 [Dagger Changelog](https://dagger.io/changelog/) 找到明确状态：**Cloud Engines 与 Cloud Checks 均为 Early Access**。因此本文件下文关于“状态未给出”的缺口判断已被更强一手证据覆盖，正式结论以 [[00_sources/briefs/2026-dagger-cloud-engines-checks|Cloud Engines 与 Cloud Checks Source Brief]] 和 [[50_deepdives/dagger/20_evidence-map|Evidence Map]] 为准。

## 研究口径

- **对象：** Dagger 开源执行层、官方 SDK/CLI、Dagger Cloud；不把第三方 CI 平台的调度能力误写为 Dagger 本体能力。
- **来源与时间：** 下列链接均为 Dagger 官方文档或 `dagger/dagger` 官方仓库/Release；访问日期均为 **2026-07-28**。文档页面未稳定展示发布日期时记为“未标示”，不能据此推断功能首次发布日期。
- **标记：** **[事实]** 为一手来源直接陈述或可检验实现；**[推断]** 为基于事实的架构判断；**[缺口]** 为当前一手资料不足以确认的内容。

## 一句话定位与分层

**[事实]** Dagger 将自身定位为软件交付自动化平台：提供“execution engine + system API”、多语言 SDK、CLI/REPL 与可复用模块；可在本地、既有 CI 或云端运行。[官方概览，未标示发布日期](https://docs.dagger.io/)

**[推断]** 对企业架构而言，它首先是一个**可编程的交付执行与编排层**，而不是事件触发、代码托管或审批制度本身。GitHub Actions、Jenkins 等仍可负责 webhook、队列、Runner 供给、身份与分支保护；Dagger 将交付逻辑表达为可复用 API，并由 Engine 解析、优化和执行。该结论由 Dagger 对 CLI/CI 的相同运行方式及 Engine 职责的明确说明支持，但并非官方给出的产品分类定义。[官方概览](https://docs.dagger.io/) [API internals，未标示发布日期](https://docs.dagger.io/reference/api/internals/)

```text
调用/触发层      本地终端 | CI job | 自定义应用 | Dagger Cloud Checks
                    │          │        │             │
接口层            Dagger CLI / Shell / SDK / 原生 GraphQL client
                    │  建立 session，提交 GraphQL 查询
模块层            Module（dagger.json）→ 自定义、可组合的 Dagger Functions
                    │  动态扩展该 session 的 GraphQL schema
执行层            Dagger Engine → DAG、惰性求值、缓存、容器/文件/服务/Secret 操作
                    │
宿主层            OCI 容器运行时或自管/托管的 Engine 基础设施
```

## 组件关系：CLI、Engine、Module、Function、SDK、GraphQL 与 DAG

- **[事实] CLI / Shell / SDK 是调用面，而非三套执行器。** Dagger API 可由 CLI、SDK、自定义 GraphQL 客户端和 HTTP/GraphQL 客户端调用；SDK 将底层 GraphQL 隐藏为语言原生的类型安全绑定。[Key Concepts，未标示发布日期](https://docs.dagger.io/getting-started/concepts/) [SDK 文档，未标示发布日期](https://docs.dagger.io/getting-started/api/sdk/)
- **[事实] GraphQL 是语言无关的低层 API；Engine 每个 session 先提供核心 GraphQL API。** 核心 API 的对象包括容器、目录、文件、服务和 Secret 等；一次请求会被转换为低层操作的有向无环图（DAG）。[API internals](https://docs.dagger.io/reference/api/internals/) [GraphQL API reference，未标示发布日期](https://docs.dagger.io/api/reference/)
- **[事实] Module 是 Functions 的可共享包装。** `dagger init` 创建含 `dagger.json` 的模块；模块加载到 session 时，Engine 拉取/复用模块源码，经 SDK 解析后动态把其 API 加入该 session 的 GraphQL schema。模块函数在 Engine 生成的容器中执行；模块自身也是连接到同一 session 的 Dagger client，可调用核心 API 与声明的依赖模块。[SDK 文档](https://docs.dagger.io/getting-started/api/sdk/) [API internals](https://docs.dagger.io/reference/api/internals/)
- **[事实] Function 是模块暴露的业务入口/组合单元，而非单一 Docker step。** Function 可返回 `Container`、`Directory`、`Service`、标量等类型；这些类型继续被链式调用，形成可求值的图。[Key Concepts](https://docs.dagger.io/getting-started/concepts/) [官方 CI quickstart，未标示发布日期](https://docs.dagger.io/getting-started/quickstarts/ci/)
- **[事实] 图采用惰性求值。** 若结果未被请求，相关 `withExec` 可被优化掉；当调用 `sync` 或请求依赖其状态的输出时才执行。官方进一步说明该机制让 Engine 优化并并行化查询执行。[API internals](https://docs.dagger.io/reference/api/internals/)
- **[推断]** 因而 Dagger 的“pipeline”更接近**类型化、可组合的声明性计算图**：代码呈现为顺序或函数调用，但 Engine 只物化被输出端需求牵引的子图，并可并发运行没有依赖边的节点；它不是按源码行序强制串行的 shell 脚本。该推断直接受 DAG 与惰性求值机制支持。

## 容器运行时与执行路径：本地、既有 CI、Cloud Checks

### 本地或既有 CI 的同构路径

1. **[事实]** 主机上的 CLI 连接 Engine；默认会探测 OCI 兼容容器运行时（文档列出 Docker、Podman、nerdctl/Finch、Apple `container`）。若没有可用 Engine，CLI 会下载与自身版本匹配的 Engine image，在容器中启动并连接。[Container runtimes，未标示发布日期](https://docs.dagger.io/reference/container-runtimes/)
2. **[事实]** CLI/SDK 打开 Engine session；此 session 有独立 GraphQL server，并负责本地目录同步、socket 代理等会话边界工作。[API internals](https://docs.dagger.io/reference/api/internals/) [Glossary，未标示发布日期](https://docs.dagger.io/reference/glossary)
3. **[事实]** Engine 加载模块、扩展 schema、把请求编译为 DAG，并在其所生成的容器内执行 Function；CLI 依据命令将结果打印、下载、打开终端或代理端口。[API internals](https://docs.dagger.io/reference/api/internals/)
4. **[事实]** 官方将同一逻辑描述为可先在本地运行、随后将 module 和 `dagger` 调用放入 CI；Checks 可在本地、任意 CI 阶段或主干合入时运行。这里“相同”指模块/API 与 Engine 模型相同，并不意味着本地与 CI 拥有相同凭据、网络、CPU、缓存保留期或审批权限。[CI quickstart](https://docs.dagger.io/getting-started/quickstarts/ci/) [官方概览](https://docs.dagger.io/)

### Dagger Cloud 的边界

- **[事实]** 用 `dagger login` 或 token 将本地/CI 运行连接到 Dagger Cloud 后，Cloud 可展示和调试这些运行的 trace；因此可观测性控制面不要求把 Engine 迁到 Cloud。[Dagger Cloud 文档，未标示发布日期](https://docs.dagger.io/reference/configuration/cloud/)
- **[事实]** Cloud 的 **Checks** 是另一种托管执行模式：GitHub App 接到 commit 后，加载根目录的 `dagger.json`、发现 check functions，在 Dagger 管理的 Engine、计算与缓存上运行，并回写 GitHub commit status。该功能要求 Dagger Cloud Team account、GitHub App 和 module/check 前置条件。[Dagger Cloud 文档](https://docs.dagger.io/reference/configuration/cloud/)
- **[推断]** “Dagger Cloud 只是观察层”已不完整：对用户自带 Engine 的 Trace 场景，它是观测/协作控制面；对 Cloud Checks，它还承担 GitHub 事件接入与托管执行面。二者应在企业评估、数据边界和责任划分中分开。
- **[主 Agent 复核]** [Dagger Changelog](https://dagger.io/changelog/) 已明确把 Cloud Checks 与 Cloud Engines 标记为 **Early Access**。区域、隔离模型、底层硬件规格、SLA、缓存留存与跨租户隔离细节仍是证据缺口；这些不能由“managed engines”一词外推。

## 缓存、内容寻址与并发：能说明什么，不能说明什么

- **[事实] 内容寻址与精确失效。** 官方称类型可跨 SDK/模块边界以 content-addressed 方式传递而无需序列化；每个 operation 以输入为键，变更一个文件只重跑受影响操作，缓存可自动用于本地与 CI。[官方概览](https://docs.dagger.io/)
- **[事实] 两级缓存不可混写。** 旧版但仍明确的官方缓存说明区分：BuildKit 实现 layer cache；Dagger 实现跨 Engine session 持久的 cache volumes。layer 的前序变化会使该层及后续层失效；cache volume 面向如包管理器下载缓存的跨运行复用。[Caching v0.17.2，发布日期未标示](https://docs.dagger.io/0.17.2/features/caching/)
- **[事实] Function cache 是更高层的调用结果缓存。** 当前文档称无 `cache` 属性的 Function 默认最大 TTL 为 7 天；cache key 包括模块源码、函数参数和父对象状态。命中时整个 Function body 不运行，但 Function miss 后其内部 container/file layer 仍可能命中；`cache="never"` 不会关闭内部 layer cache。文档注明该可配置行为对 v0.19.4 后适用，旧模块为兼容保留 session 行为直到调整设置。[Function Caching，发布日期未标示](https://docs.dagger.io/extending/function-caching/)
- **[事实] 并发来自依赖图而非“无上限并行”。** 官方文档说明 Engine 将请求转为 DAG，并利用惰性求值优化和并行化；早期官方 CI 教程也明确说把阶段与依赖表示成 DAG 后并发运行，以提高速度。最新 v0.21.7 release 还记录了为高容器并发扩大默认 CNI IP pool 的变更，表明并发是实际运行时约束之一。[API internals](https://docs.dagger.io/reference/api/internals/) [CI tutorial v0.17.2，发布日期未标示](https://docs.dagger.io/0.17.2/ci/quickstart/env/) [v0.21.7 release，2026-06-17](https://github.com/dagger/dagger/releases/tag/v0.21.7)
- **[推断]** Dagger 的性能模型是“**显式输入 × 图切分 × 多层缓存 × 依赖就绪并发**”，而不是简单的 Docker layer cache 包装。把全仓目录、当前时间、隐式环境或外部可变数据塞进 Function，会扩大 cache key 或引入不可复现性；应以窄而显式的 `Directory`/参数作为输入，并对网络数据设置 TTL 或显式禁用缓存。这一判断由 Function cache key 和类型系统文档支撑。[Function Caching](https://docs.dagger.io/extending/function-caching/) [Type-system guidance，发布日期未标示](https://docs.dagger.io/next/extending/type-system/constructors-fields-methods/)
- **[缺口]** 官方资料未在本研究范围内提供可用于企业容量规划的公开 benchmark（命中率、跨机 cache transfer 吞吐、并行调度上限、缓存命中/未命中 P95）。不能将“内容寻址/并发”表述成任何确定百分比的加速收益。

## SDK、语言与产品状态

**[事实]** 当前官方概览称有 **8 种语言 SDK**；`main/sdk` 目录可核验 `.NET、Elixir、Go、Java、PHP、Python、Rust、TypeScript`，另有 CUE 目录。当前文档中的 `dagger init` 示例列出 Go、Python、TypeScript、PHP、Java；不能因为示例只展示五种就否定仓库内八种 SDK 的存在。[官方概览](https://docs.dagger.io/) [`sdk/` 源码目录，访问 2026-07-28](https://github.com/dagger/dagger/tree/main/sdk) [SDK 文档](https://docs.dagger.io/getting-started/api/sdk/)

**[事实]** v0.21.7 的官方 release 同时列出 Go、Python、TypeScript、PHP、Rust、Elixir SDK 发布物，并明确这些发布物使用 v0.21.7 Engine/CLI；这能证明这些语言在该 release 中有可检验的同步版本制品。[官方 release 总览，2026-06-17](https://github.com/dagger/dagger/releases/tag/v0.21.7)

**[缺口]** 官方首页的“8 SDK”、源码目录与 v0.21.7 发布条目不足以精确判定每种语言的稳定性承诺、支持等级、弃用政策或各语言是否完全功能对等。尤其 .NET/Java 本次没有在同一 release 页面看到同名 SDK release 条目；应标为“仓库存在/官方称支持”，而非擅自标成 GA、Experimental 或 Community。

## 版本与发布节奏

- **[事实] 当前版本：** 截至访问日，`dagger/dagger` GitHub Releases 的 Latest 指向 **v0.21.7**，发布日期 **2026-06-17**；其 release note 包含 Engine、CLI、Go SDK、Apple container driver、CNI 并发与运行时修复，说明该主版本是跨组件发布而非仅 CLI 包。[v0.21.7 release](https://github.com/dagger/dagger/releases/tag/v0.21.7)
- **[事实] 最近节奏样本：** 同一官方 release 列表显示 v0.21.6 发布于 **2026-06-10**，v0.21.7 于 **2026-06-17**；两个相邻 patch 相隔约一周。[Releases list](https://github.com/dagger/dagger/releases)
- **[推断]** 这表明 2026 年 6 月至少存在周级 patch 发布节奏，但两个版本不能证明固定周更、SLA 或未来 cadence。企业应锁定兼容的 CLI/Engine/SDK 版本，并在 `dagger develop` 前审阅 breaking changes；这是官方 SDK 文档明确给出的兼容性提示。[SDK 文档](https://docs.dagger.io/getting-started/api/sdk/)

## 对 CI/CD 平台决策的含义

1. **[推断] 可移植性来自执行模型，非自动消除环境差异。** 本地与 CI 可调用相同 module 和 Engine API；但发布凭据、出口网络、制品仓权限、资源配额与保护规则仍必须在调用环境中单独配置和审计。
2. **[推断] Dagger 适合承载“构建—测试—制品—发布”的可组合实现，但不能替代外部授权与独立 Oracle。** Engine 能执行容器操作和回传结果；它不天然证明生产变更被授权、制品合规、SLO 达标或业务正确。此为控制边界判断，不是 Dagger 产品宣称。
3. **[推断] 评估 Cloud 时要先选模式。** 只接入 trace 时，主要评估 telemetry/访问控制；采用 Cloud Checks 时，还要评估 GitHub App 权限、托管计算、缓存、密钥注入和状态回写。

## 事实审计结论

可进入专题正式结论的表述：

- Dagger 是以 Engine + GraphQL 系统 API 为核心、通过 CLI/SDK/Module 暴露的交付执行与编排平台；Function 被装入 Module，在 Engine 容器中运行，Module 会动态扩展 session API。
- Dagger 以 DAG、惰性求值、内容寻址和分层缓存实现增量执行；独立无依赖节点可并发，但性能收益依赖输入边界、缓存命中和宿主资源。
- Dagger Cloud 对自管 Engine 可提供 trace/协作，对 Cloud Checks 则提供 GitHub 接入和托管 Engine 执行；两种模式不能混为一个“纯观测服务”。
- 截至 2026-07-28 可核验的 Latest 为 v0.21.7（2026-06-17）；官方称 8 种 SDK，源码目录可见 .NET、Elixir、Go、Java、PHP、Python、Rust、TypeScript。

不得进入正式结论的表述：

- “Dagger 替代 GitHub Actions/Jenkins”或“本地与 CI 的权限、网络和缓存完全一致”；
- “所有 8 种 SDK 功能成熟度相同/均为 GA”；
- “Dagger Cloud 仅是 observability”“Cloud Checks 已 GA”或“Cloud Checks 的隔离/SLA 已由公开资料证明”；
- “Dagger 的缓存必然将流水线加速某一百分比”。
