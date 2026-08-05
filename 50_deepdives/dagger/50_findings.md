---
title: Dagger 专题分析发现
tags:
  - research/agentic-cicd
  - research/findings
  - tool/dagger
status: complete
as_of: 2026-08-02
confidence: high
---

# Dagger 专题分析发现

## F1：Dagger 的核心创新是执行图，不是“少写 YAML”

**事实：** Function 接收和返回类型化对象，Engine 把调用编译成惰性求值的 DAG，并通过内容寻址、缓存和依赖就绪并发执行。见 [[00_sources/briefs/2026-dagger-engine-modules-execution|Engine、Module 与增量执行]]。

**分析：** 把 Dagger 仅理解成 Pipeline-as-Code，会错过它真正的架构价值：交付逻辑从 CI Provider 的 Step 列表变成可以组合、传递和继续求值的 API 对象。代码语言只是入口，Engine 的图语义才是差异化。

## F2：Dagger 解耦了执行逻辑与 CI 控制面，但 Cloud 正在重新把两者组合

**事实：** Hybrid 模式中，GitHub Actions、GitLab 或 Jenkins 仍负责事件、Runner、Token 与结果状态；Dagger 在 Job 内执行。Cloud Checks 则监听 Git Commit、在托管 Engine 上运行 Check 并回写状态，但截至 2026-08-02 仍按明确状态证据标记为 Early Access。见 [[00_sources/briefs/2026-dagger-cloud-engines-checks|Cloud Engines 与 Cloud Checks]]。

**分析：** “Dagger 不替代 CI”与“Dagger 能替代 CI”都只说对了一半。当前最稳妥的表达是：**开源 Engine 是可嵌入的数据面；Dagger Cloud 正以 EA 产品向 CI 控制面扩张。**

## F3：本地与 CI 一致，准确含义是“程序合同一致”

相同 Module、Function、类型和 Engine 模型可以在本地与 CI 调用，这显著改善 Pipeline 调试。但以下变量不会被自动抹平：

- CI Token、OIDC 与生产 IAM；
- Linux Runtime、CPU 架构与内核能力；
- DNS、Proxy、证书、网络出口与内部服务；
- Cache 位置、命中率、GC 与跨节点传输；
- Git Event、Branch Protection、Environment Approval；
- 外部 API、Registry、Artifact 与部署目标状态。

因此，正确主张是“**执行逻辑可移植**”，不是“**环境完全相同**”。

## F4：缓存不是透明加速器，而是需要设计的业务语义

Function Cache 可以让整个函数体不再执行。对纯构建和确定性转换，这是强大优势；对发布、部署、通知、随机数、时间和远程状态读取，它可能直接改变业务结果。

平台团队必须同时设计：

- 输入是否足够窄、完整且显式；
- 选择最大 TTL、有限 TTL、Session 还是 `never`；
- Function Cache 与内部 Layer/Volume Cache 的关系；
- Cold/Hot Cache、跨 Engine 传输和 GC；
- Cache 是否跨 Repo、环境或租户共享。

Airbyte、Discern 与 Flipt 的案例显示热缓存可能产生巨大收益，也同时证明不能用热缓存结果代表所有运行。见 [[50_deepdives/dagger/30_case-map|案例地图]]。

## F5：Dagger 类型系统兼具数据类型与能力类型，但不是 IAM

`Directory`、`Secret`、`Socket`、`Service` 不是普通字符串；它们把宿主资源的进入点显式化。这个设计减少了脚本从当前目录、全局环境和本地 Socket 偷偷获得能力的机会。

但显式传入不等于安全批准：

- 传入整个仓库仍可能暴露无关机密；
- 传入 Docker Socket 近似委托宿主控制能力；
- 传入生产 Secret 仍允许获准代码使用它；
- 顶层 Module 可以把能力继续传给依赖；
- 网络与制品输出可能成为数据外泄通道。

类型是能力边界的表达方式，IAM、Policy、Egress 和审计仍在外部。

## F6：Dagger 的安全模型必须画成“双层沙箱”

Function 默认看不到宿主资源，这是第一层；Engine 当前仍需特权容器、默认允许 `insecureRootCapabilities` 且不支持 Rootless，这是第二层。见 [[00_sources/briefs/2026-dagger-sandbox-secrets-engine-security|安全边界 Source Brief]]。

这两个事实并不互相否定，但会导出明确的企业结论：

> [!warning] 关键边界
> Dagger Function 可以是“默认宿主盲”的，同时承载它的 Engine 仍是高权限基础设施。前者改善 Module 能力传播，后者决定不可信多租户隔离上限。

Puzzle 采用 KubeVirt VM 的案例说明，这不是理论问题，而是会改变团队权限、节点架构与运维成本的部署约束。

## F7：Dagger Cloud 是体验放大器，也是主要锁定面

开源 Engine、Module 源码和多 CI Hybrid 模式降低了对单一 CI Provider 的依赖；Dagger Cloud 进一步提供 Trace、Module 可见性、分布式 Cache、托管 Engine 和 Cloud Checks。

价值与锁定来自同一组能力：

| 价值 | 对应锁定面 |
|---|---|
| 跨本地/CI Trace | Telemetry 数据、角色与保留 |
| 分布式 Cache | Cache 格式、传输、容量与出口 |
| 托管 Engine | 计算区域、隔离、SLA 与计费 |
| Cloud Checks | GitHub App、事件与 Commit Status |
| Module Catalog | Git Source 扫描、元数据与组织治理 |

“核心开源”降低退出难度，但不能证明 Cloud Cache、Trace 和托管 CI 可以无损迁移。

## F8：LLM Primitive 的价值是把概率性判断接入确定性执行图

LLM 可发现 Env 中的 Function，调用 Container、Directory、Module 与 MCP Server，并把 Prompt、Tool Call 和底层操作留在 Trace 中。见 [[00_sources/briefs/2026-dagger-llm-mcp-runtime|LLM 与 MCP Source Brief]]。

这使 Dagger 成为 Agentic CI/CD 的有价值执行底座，但它没有改变四条控制原则：

1. Tool 可见性与当前任务获准执行的动作必须分开；
2. Agent 不应拥有修改成功标准和批准自身结果的权限；
3. 写动作应进入 Changeset、Draft PR、Plan 或受控 Pipeline；
4. Test、Scan、Policy、Signature、SLO 和人工 Review 才是最终 Oracle。

由于 LLM/Env 仍未稳定且缺少独立效果数据，当前最合理的自治上限是只读分析、候选变更和非生产受控验证，而非自动生产发布。

## F9：Dagger 最适合成为平台产品，不适合成为“每个仓库的新脚本语言”

采用收益与复用半径正相关。以下条件越多，Dagger 越值得：

- 多语言或 Monorepo；
- 多 CI Provider 或 Runner 频繁变化；
- 复杂集成测试与临时服务；
- 大量重复构建、测试和制品逻辑；
- 平台团队能维护 Module API、版本、Owner 和 SLO；
- 本地复现 CI 是高频痛点。

若只是一个仓库、三五个稳定 Step、没有共享能力和 Cache 痛点，Make/npm scripts/原生 CI 往往更深、更简单。Dagger 不应因“现代”而被默认引入。

## F10：最优迁移模式是绞杀式迁移，不是重写式迁移

企业应先把一个高频、纯函数化、易验证的 Check 或 Build 抽成 Dagger Module，保留原 CI Event、Required Check 和身份。只有在以下条件成立后，才扩大到共享 Module、远程 Engine 或 Cloud：

- 本地与 CI 结果一致性达到内部门槛；
- Cold/Hot Cache 和单位成功成本优于基线；
- Engine 特权、网络、Secret 与 Cache 边界已审计；
- Module 版本与升级可回滚；
- 失败仍能回到原 CI 实现；
- Cloud Early Access 能力不成为关键发布单点。
