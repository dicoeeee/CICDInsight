---
title: "Buildkite 发展史：从混合 CI 执行面到 Agentic CI（2026-08-03）"
tags:
  - research/cicd
  - company/buildkite
  - history
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# Buildkite 发展史：从混合 CI 执行面到 Agentic CI（2026-08-03）

> [!info] 研究范围与证据规则
>
> 本文只使用 Buildkite 官方 About、博客、Changelog、文档、官方 GitHub 仓库与公司官方新闻稿；没有用 Wikipedia、媒体报道或搜索摘要作为关键证据。每条来源均于 **2026-08-03** 访问；页面没有给出发布日期时，明确标为“页面未标日期”。“首次发布”只在官方公告明确说 `introduced`、`released` 或给出同期发布记录时使用；当前文档只能证明“截至本次访问存在”，不能倒推首次上线日期。

## 写作提纲

1. 核对创始年份、创始人和创办前经历；
2. 用已标日期的官方公告建立产品演进时间轴；
3. 区分原生 Agent/混合架构、托管执行面、测试产品和 Agentic CI；
4. 只收录可由官方材料核验的融资、收购及组织变化；
5. 记录相互矛盾的官方融资金额与未能补齐的历史缺口。

## 结论先行

**来源明确事实。** Buildkite 官方 About 页称公司创立于 2013 年；Keith Pitt 于同年写出最初版本，Tim Lucas 与 Keith 共同创办，Lachlan Donald 是早期投资人后加入为联合创始人。早期公开、可定年的产品里程碑是 2016 年的 Elastic CI Stack for AWS：在客户 AWS VPC 内运行可自动扩缩的 Buildkite Agent 集群。当前官方架构文档将这一模式定义为 **SaaS 控制面 + 客户自管 Agent 执行面** 的 hybrid architecture。2023 年又增加 Kubernetes Agent Stack，2024 年推出 Buildkite-hosted agents 与 Test Engine，2025 年以 MCP、model providers、SDK 和 AI plugins 组成的 agentic workflow components 进入产品叙事。[About](https://buildkite.com/about/company/) [Elastic CI Stack](https://buildkite.com/resources/blog/introducing-a-new-continuous-integration-stack-for-aws-the-elastic-ci-stack/) [架构文档](https://buildkite.com/docs/pipelines/architecture) [Scale-Out 平台公告](https://buildkite.com/resources/blog/introducing-the-scale-out-delivery-platform/) [Agentic CI 公告](https://buildkite.com/resources/blog/what-ai-is-teaching-us-about-ci/)

**分析性归纳（非“首次技术发明”主张）。** 这条轨迹不是把自管 Agent 替换成托管 Runner，而是从“托管协调、客户掌控执行面”的混合 CI，扩展为可选的托管执行面、Kubernetes/AWS 执行栈、跨构建测试数据层，以及面向 AI Agent 的工具与受控 Pipeline Step。它仍不是完全自托管控制面的产品：官方文档明确将 Pipelines 定义为 SaaS control plane。

## 时间轴与逐项核验

| 时间 | 已核验事件 | 能证明什么 | 来源发布日期 / 访问日期 | 证据边界 |
|---|---|---|---|---|
| 2013 | 官方 About 页称 Buildkite 创立于 2013；Keith Pitt 在机场候机楼写出 original Buildkite。Tim Lucas 与 Keith 于 2013 年共同创办。 | 创立年份及 Keith、Tim 的创办关系。 | About：**页面未标日期**；访问：2026-08-03。[About](https://buildkite.com/about/company/) | 该页称 Lachlan 为联合创始人，且说他是早期投资后加入；但未给出他加入的年月，不能写成“2013 三人同时创立”。 |
| 2013 以前 / 早期背景 | Keith 曾创办并出售 Desktoppr，并任职 Envato、Qantas 工程领导岗位；Tim 曾任 Pin Payments 高级软件工程师、Amen 前端/网页应用工程负责人，且经营过悉尼设计工作室；Lachlan 曾任 99designs founding CTO。 | 创始团队与产品、支付、设计和大规模开发工具/基础设施的早期履历。 | About：**页面未标日期**；访问：2026-08-03。[About](https://buildkite.com/about/company/) | 均为公司自述；未用第三方履历或媒体材料扩写。 |
| 2016-07-19 | Buildkite 发布 **Elastic CI Stack for AWS**：在自有 AWS VPC 部署预构建 CI/CD stack，提供自动扩缩 Buildkite Agent cluster；公告同时说明其预装 Agent、Docker、AWS CLI、S3 与 CloudWatch 等组件，并开源。 | 最早在本轮找到且可精确日期化的“Agent 集群 + 客户自有云账户/VPC + 弹性执行”的官方里程碑。 | 博客：2016-07-19（页面显示 2024-05-02 更新）；访问：2026-08-03。[公告](https://buildkite.com/resources/blog/introducing-a-new-continuous-integration-stack-for-aws-the-elastic-ci-stack/) | 它证明该模式最迟在此日已公开，不证明 Agent 或混合架构首次出现于 2016。公告中 Shopify 作业量是客户案例陈述，未作为行业规模结论。 |
| 2020 | About 页称 Buildkite 获 **US$28M Series A**，由 OpenView 领投、General Catalyst 参与。 | 可由公司当前官方页核验的 A 轮存在、金额及领投/参与方。 | About：**页面未标日期**；访问：2026-08-03。[About](https://buildkite.com/about/company/) | 未找到同期官方新闻稿，故不记录精确公告日、估值或资金用途。 |
| 2021 | About 页称推出第二个产品 **Test Analytics**。 | 测试数据/分析成为 Pipelines 之外的产品方向。 | About：**页面未标日期**；访问：2026-08-03。[About](https://buildkite.com/about/company/) | 当前页将其称为 Test Analytics；不能仅凭此页把 2021 的功能集等同于 2024 的 Test Engine。 |
| 2022 | 关于 B 轮，Buildkite 自有页面出现互相冲突的金额：当前 About 页称 **US$31M Series B**（OneVentures、AirTree 领投）；仍在线的 Series B 新闻稿标题和正文称 **US$21M**，并称累计融资 US$39M，参与方还包括 General Catalyst 与 Dom Pym。 | B 轮及上述投资方关系有官方来源；金额**存在官方冲突**。 | About：**页面未标日期**；Series B 新闻稿：**页面未标日期**；访问：2026-08-03。[About](https://buildkite.com/about/company/) [Series B 新闻稿](https://buildkite.com/about/press/buildkite-raises-usd-21m-series-b/) | 不把 US$21M 或 US$31M 写成确定金额，也不以两页数字推导总融资额、估值或轮次顺序。需取得带发布日期的原始融资公告或投资方新闻稿后再解除冲突。 |
| 2023-03-28 | Changelog 宣布 **Agent Stack for Kubernetes**：让 Kubernetes cluster 原生将 Buildkite Pipeline steps 编排为 Kubernetes jobs。 | 自管执行面从 AWS Elastic Stack 延展到 Kubernetes 原生 job 编排。 | Changelog：2023-03-28；访问：2026-08-03。[公告](https://buildkite.com/resources/changelog/page/9/) | 当日公告用 “released”，但未在该页标 GA/Preview；不能把它外推为所有 Kubernetes 模式的成熟度承诺。 |
| 2023-09-19 | Buildkite 宣布以全股票交易签署收购 Packagecloud 的最终协议；目标是把 package repository support 纳入产品线，并称该能力计划在年底前以早期访问方式提供。公告还称年初任命 Barry Crist 为董事长、Adam Gross 为董事。 | 可核验的组织/产品边界变化：从 CI/CD 与测试扩展到包管理；同时有两项董事会任命的公司公告。 | 官方新闻稿：2023-09-19；访问：2026-08-03。[Packagecloud 公告](https://buildkite.com/about/press/buildkite-announces-acquisition-of-packagecloud/) | “entered into a definitive agreement”不等于本轮已核验交割完成日；不记录交易金额。董事任命只证明公告所述时间点，不能推断当前董事会构成。 |
| 2024 Q1 | Q1 Release 宣布 **Buildkite hosted agents**，可在 Mac 与 Linux 上使用，客户可在 self-hosted 与 Buildkite-hosted agents 间选择。当前架构文档说明 hosted architecture 由 Buildkite 同时提供 control plane 与 build environment；hosted job 使用按需、完成即销毁的 ephemeral agent。 | 在原混合模式外，增加 Buildkite 管理的执行面；并存而非替换自管 Agent。 | Q1 Release：标为 “Q1 2024”，**页面未标精确日期**；架构/Hosted Agent 文档：**页面未标日期**；访问：2026-08-03。[Q1 Release](https://buildkite.com/resources/releases/2024-q1/) [架构文档](https://buildkite.com/docs/pipelines/architecture) [Hosted agents 文档](https://buildkite.com/docs/agent/buildkite-hosted) | 未将“Q1 2024”细化为某日；也不把当前产品页的性能/成本宣传当作普遍基准。 |
| 2024-10-09 | Buildkite 发布 Scale-Out Delivery Platform，称其包含 Pipelines、**Test Engine**、Package Registries 和 Mobile Delivery Cloud；公告以“Today we’re excited to introduce Buildkite Test Engine”明确介绍 Test Engine，功能包括实时 flaky-test 管理、智能 test splitting、test ownership/assignment。 | Test Engine 作为独立产品品牌及初始功能集的正式公开节点。 | 博客：2024-10-09；访问：2026-08-03。[公告](https://buildkite.com/resources/blog/introducing-the-scale-out-delivery-platform/) | 不将厂商“dramatically reduce”或“any scale”等宣传语转写为实测成效；Test Engine 当前功能还会继续演化。 |
| 2025-10-02 | Buildkite 为 Test Engine 发布 Workflows，支持多种 flaky 检测启发式、自动标签/状态变更、Slack/webhook 与 Linear 操作；公告标为 public preview。 | Test Engine 从“检测/切分/归属”扩展到可配置的测试问题响应工作流。 | 博客：2025-10-02；访问：2026-08-03。[公告](https://buildkite.com/resources/blog/introducing-test-engine-workflows/) | 仅 Workflows 在该公告中明确 public preview，不能推断整个 Test Engine 的统一产品阶段。 |
| 2025-12-01 | 官方博客宣布五个 **agentic workflow components**：remote-friendly MCP server、model providers、universal pipeline triggers、可在通用语言中生成 dynamic pipelines 的 SDK、以及 AI plugins。配套文档把能力分为“用 Agent 构建/维护 Pipeline”与“在 Pipeline step 内运行 Agent”。 | Buildkite 将 AI 接入定位为可组合的 CI 工作流组件，而非把 Agent 等同于自动发布权。 | 博客：2025-12-01（2025-12-03 更新）；文档：**页面未标日期**；访问：2026-08-03。[公告](https://buildkite.com/resources/blog/what-ai-is-teaching-us-about-ci/) [AI agents 文档](https://buildkite.com/docs/platform/ai-agents) | 博客称“今天宣布”这些组件，但不分别证明每个底层项目的首次代码提交/首次可用日期。MCP 能触发 run，实际可做操作仍由 token scope 与平台权限约束。 |
| 2026-04-30 / 2026-06-12 | Changelog 发布面向 Claude Code、Cursor 等的官方 Buildkite Skills；后续为 headless/background agent 增加 Remote MCP API token endpoint，并明确标为 **Preview**。 | Agentic CI 从 2025 的组件宣布继续向 Agent 使用体验与后台认证模式迭代。 | Changelog：2026-04-30、2026-06-12；访问：2026-08-03。[Skills 公告](https://buildkite.com/resources/changelog/page/2/) [Remote MCP Preview](https://buildkite.com/resources/changelog/363-remote-mcp-server-api-access-token-support-is-now-available-preview/) | API-token Remote MCP 仅是 Preview；不能把它或 MCP 可调用性写成已获得合并、发布、部署或绕过审批的授权。 |

## 产品与架构演进：可成立的分层叙事

### 1. 早期核心：Pipelines 协调 + 客户控制 Agent 执行环境

截至本轮能精确日期化的最早证据是 2016 年 Elastic CI Stack。它将 Buildkite Agent cluster 部署在客户 AWS VPC，并可依 build queue 自动扩缩；这与当前文档所定义的 hybrid architecture 一致：Pipelines 是 SaaS control plane，负责协调与展示结果，客户在 on-prem 或云中运行执行 job 的 Agent。[2016 Elastic CI Stack](https://buildkite.com/resources/blog/introducing-a-new-continuous-integration-stack-for-aws-the-elastic-ci-stack/) [Pipelines architecture](https://buildkite.com/docs/pipelines/architecture)

当前官方 GitHub 仓库将 Buildkite Agent 描述为“用 Go 编写、可在任意设备或网络安全运行 build jobs 的开源 toolkit”；自管 Agent 文档说明客户承担 provisioning、scaling、security 与维护。这些是当前产品事实，不构成 2013 年首版 Agent 的发布日期证明。[Agent GitHub](https://github.com/buildkite/agent) [Self-hosted agents](https://buildkite.com/docs/agent/self-hosted)

### 2. 从固定 YAML 到运行时生成的任务图：Dynamic Pipelines

当前 Dynamic Pipelines 文档证明：脚本可在 build time 生成 YAML 或 JSON steps，并通过 `pipeline upload` 加入同一个 build；每个生成 step 成为独立 job，可按 `agents` query 或 queue 路由到不同 Agent。Buildkite 也提供 SDK，用 JavaScript/TypeScript、Python、Go、Ruby 等语言构建类型安全、可单测的定义。[Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines)

这使 Pipeline 从静态配置扩展为**运行时构造 CI job 图并路由到异构执行面**的编排层；但它不是 Bazel/Dagger 那类 build graph/runtime 的替代，也不证明 Dynamic Pipelines 的首次可用年份。本轮找到的 Buildkite 官方动态 Pipeline 说明文章发布日期为 2023-11-10（2025-03-28 更新），属于后续阐释，不能据此标为功能首发。[The power of Dynamic Pipelines](https://buildkite.com/resources/blog/how-to-build-ci-cd-pipelines-dynamically/)

### 3. 执行面扩展：AWS Elastic Stack、Kubernetes Agent Stack、Hosted Agents 并存

- **Elastic CI Stack for AWS（2016）**：自有 VPC 内的自动扩缩 Agent cluster，是私有 AWS 执行面的预构建栈。
- **Agent Stack for Kubernetes（2023）**：由 Kubernetes 将 Pipeline steps 原生编排为 Kubernetes jobs。
- **Buildkite hosted agents（2024 Q1）**：由 Buildkite 管理 provision、scaling、底层服务器；当前文档称每个 hosted job 在新的 ephemeral Agent 上开始，并在完成后销毁。

因此，产品演进不是“self-hosted 已被 hosted 替代”，而是自管 AWS/Kubernetes/通用 Agent 与托管 Mac/Linux 执行面共存。控制面仍由 Buildkite SaaS 提供；要求控制面也在企业环境内运行的场景，不应把 Buildkite 描述为完全 self-managed。[架构文档](https://buildkite.com/docs/pipelines/architecture) [Hosted agents 文档](https://buildkite.com/docs/agent/buildkite-hosted)

### 4. 测试成为独立数据与响应层：Test Analytics → Test Engine

官方 About 页将 2021 Test Analytics 列为第二产品；2024-10-09 的公告再明确推出 Test Engine，并说明其初始重点是 flaky test 管理、智能分割与责任归属。2025 Test Engine Workflows 把检测结果接到可配置 action（标记、mute/skip、通知、建单），且当时为 public preview。这里可写成“测试能力从分析产品发展为测试执行数据/决策层”，但不能将所有功能追溯为 2021 已有。[About](https://buildkite.com/about/company/) [Test Engine 公告](https://buildkite.com/resources/blog/introducing-the-scale-out-delivery-platform/) [Workflows 公告](https://buildkite.com/resources/blog/introducing-test-engine-workflows/)

### 5. Agentic CI：Agent 使用 CI，也在 CI 内运行

截至 2026-08-03，官方文档将 AI 能力分为两条路径：

- **Build with agents**：Skills 提供 Pipeline、CLI/API、迁移与运行时知识；MCP Server 让外部 AI agent 实时查看 build state、读日志、触发 run、迭代配置。
- **Use agents in CI**：model providers 将 LLM 接入 Pipeline steps，使其使用 build log、artifact、security policy 与实时 Pipeline data；AI plugins 可在失败时进行分析和日志总结。

这说明 Buildkite 已提供 Agentic CI 的受控集成面，但不等于 Agent 获得代码合并、生产发布或任何越权能力。截至访问日，model providers 文档只明确支持 Anthropic models；API-token 的 headless Remote MCP endpoint 明确仍为 Preview。[AI agents 文档](https://buildkite.com/docs/platform/ai-agents) [Remote MCP Preview](https://buildkite.com/resources/changelog/363-remote-mcp-server-api-access-token-support-is-now-available-preview/)

## 融资与组织变化：只保留可核验项

| 项目 | 可以写入的事实 | 不可写入 / 待核验 |
|---|---|---|
| 创始团队 | Keith 是 Founder；Tim 是与 Keith 于 2013 共同创办的 Co-founder；Lachlan 是早期投资后加入的 Co-founder。 | 三人各自加入的完整日期、股权比例、最初公司法定设立日期。 |
| 融资 | 官方 About 页记录 2020 年 US$28M Series A；官方页面都承认 2022 年有 OneVentures 与 AirTree 主导的 Series B。 | B 轮确定金额：About 为 US$31M，保留的新闻稿为 US$21M；存在冲突，不下结论。未找到同期开源/投资方一手公告可消解。 |
| 领导层 | Tim 官方简介称其 co-CEO/CMO 任至 2022；当前 About 页列 Kevin Gounden 为 CEO、Lachlan Donald 为 CTO；2023 新闻稿记录 Barry Crist 任董事长、Adam Gross 任董事。 | CEO/CTO 交接的精确日期与完整过程；2023 后的董事会现状。 |
| 收购 | 2023-09-19 官方公告称已签署 Packagecloud 收购最终协议，且为全股票交易。 | 交割完成的日期、交易价值与整合后的财务影响。 |

## 证据缺口与不应外推的结论

1. **2013—2015 的产品首发史缺口。** 本轮仅从官方 About 核实“original Buildkite in 2013”，最早有精确日期的 Agent 集群公告是 2016；未找到首个 Agent、首个 Pipeline、Dynamic Pipelines 或 SaaS 控制面首次发布的官方日期。不得把 2016 写成 Agent/混合架构诞生年。
2. **Dynamic Pipelines 首发日期缺口。** 当前文档充分证明机制，2023 博客充分解释用法，但都不证明最初上线日。
3. **融资金额冲突。** 同一公司官网的 About 与 Series B 新闻稿分别给出 US$31M/US$21M；在原始同期公告或投资方一手新闻稿出现前，金额保持 `conflicting`。
4. **组织交接日期缺口。** About 页说明了 Tim 的任期与当前 CEO/CTO，但没有给出任命或离任日期；不能补写具体年份、原因或治理安排。
5. **产品状态边界。** 当前文档存在不等于 GA；本轮只将 Test Engine Workflows 与 API-token Remote MCP 明确标为 Preview。各 Hosted Agents、Agent Stack、Dynamic Pipelines、Test Engine 的 GA/Preview 状态需要逐一依据对应 release/changelog 复核。
6. **能力不等于授权。** MCP 可读取日志或触发 run、model providers 可用于 Pipeline step，不自动授予 merge、release、deploy 或跨系统写权限；仍受 token scope、组织权限、Pipeline 配置与外部控制约束。

## 来源清单

| 来源 | 类型 | 发布日期 | 访问日期 | 用途 |
|---|---|---:|---:|---|
| [Buildkite About](https://buildkite.com/about/company/) | Buildkite 官方 About | 页面未标日期 | 2026-08-03 | 创始年份、创始团队履历、2020/2021/2022 公司自述、当前/历史领导信息。 |
| [Elastic CI Stack for AWS](https://buildkite.com/resources/blog/introducing-a-new-continuous-integration-stack-for-aws-the-elastic-ci-stack/) | Buildkite 官方博客 | 2016-07-19；更新 2024-05-02 | 2026-08-03 | 最早可定年的 Agent 集群/客户 VPC 弹性执行面。 |
| [Pipelines architecture](https://buildkite.com/docs/pipelines/architecture) | Buildkite 官方文档 | 页面未标日期 | 2026-08-03 | SaaS control plane、自管 Agent 与 hosted architecture 的边界。 |
| [Self-hosted agents](https://buildkite.com/docs/agent/self-hosted) | Buildkite 官方文档 | 页面未标日期 | 2026-08-03 | 客户对执行面 provisioning/scaling/security/maintenance 的责任。 |
| [Buildkite Agent](https://github.com/buildkite/agent) | Buildkite 官方 GitHub 仓库 | 页面未标日期 | 2026-08-03 | Agent 为开源 Go toolkit 的当前事实。 |
| [Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) | Buildkite 官方文档 | 页面未标日期 | 2026-08-03 | 运行时生成、上传与路由 job 的机制。 |
| [The power of Dynamic Pipelines](https://buildkite.com/resources/blog/how-to-build-ci-cd-pipelines-dynamically/) | Buildkite 官方博客 | 2023-11-10；更新 2025-03-28 | 2026-08-03 | 后续产品阐释，不用作首发日期。 |
| [Agent Stack for Kubernetes](https://buildkite.com/resources/changelog/page/9/) | Buildkite 官方 Changelog | 2023-03-28 | 2026-08-03 | Kubernetes jobs 编排的发布记录。 |
| [Packagecloud acquisition](https://buildkite.com/about/press/buildkite-announces-acquisition-of-packagecloud/) | Buildkite 官方新闻稿 | 2023-09-19 | 2026-08-03 | 最终协议、全股票交易、董事会任命。 |
| [Q1 2024 Release: Hosted agents](https://buildkite.com/resources/releases/2024-q1/) | Buildkite 官方 Release | Q1 2024（页面未标精确日期） | 2026-08-03 | Hosted Agents 发布。 |
| [Scale-Out Delivery Platform](https://buildkite.com/resources/blog/introducing-the-scale-out-delivery-platform/) | Buildkite 官方博客 | 2024-10-09 | 2026-08-03 | Test Engine 正式介绍及产品组合。 |
| [Test Engine Workflows](https://buildkite.com/resources/blog/introducing-test-engine-workflows/) | Buildkite 官方博客 | 2025-10-02 | 2026-08-03 | Workflows 功能与 public preview 状态。 |
| [What AI is teaching us about CI](https://buildkite.com/resources/blog/what-ai-is-teaching-us-about-ci/) | Buildkite 官方博客 | 2025-12-01；更新 2025-12-03 | 2026-08-03 | 五项 agentic workflow components。 |
| [AI agents in Pipelines](https://buildkite.com/docs/platform/ai-agents) | Buildkite 官方文档 | 页面未标日期 | 2026-08-03 | Agent 使用 CI / 在 CI 中运行、model provider 与插件边界。 |
| [Official Buildkite skills](https://buildkite.com/resources/changelog/page/2/) | Buildkite 官方 Changelog | 2026-04-30 | 2026-08-03 | Skills 发布。 |
| [Remote MCP API-token support](https://buildkite.com/resources/changelog/363-remote-mcp-server-api-access-token-support-is-now-available-preview/) | Buildkite 官方 Changelog | 2026-06-12 | 2026-08-03 | Headless Remote MCP 为 Preview。 |
| [Series B official press page](https://buildkite.com/about/press/buildkite-raises-usd-21m-series-b/) | Buildkite 官方新闻稿 | 页面未标日期 | 2026-08-03 | 与 About 页冲突的 US$21M B 轮金额、投资方与累计融资说法。 |

## 供主 agent 整合（≤150字）

Buildkite 始于 2013，核心演进是 SaaS 控制面协调客户自管 Agent，后扩展 AWS/Kubernetes 执行栈、托管 Agents、Test Engine 与 Agentic CI。Dynamic Pipelines 让运行时生成任务图；B 轮金额官方页冲突（US$21M/31M），应保留待核验。
