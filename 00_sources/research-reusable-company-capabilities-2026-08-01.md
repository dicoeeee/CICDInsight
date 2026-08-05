---
title: 可迁移 Agentic CI/CD 公司能力组合预研（2026-08-01）
tags:
  - research/agentic-cicd
  - research/topic-selection
status: pre-research
as_of: 2026-08-01
---

# 可迁移 Agentic CI/CD 公司能力组合预研（2026-08-01）

> [!warning] 选题预研，非 Source Brief，不得直接进入正式 Presentation
>
> 本文只用于选择下一项 Deep Dive 与单页主张；不替代逐主张事实审计或 `presentation_ready` 门禁。全部链接均为官方一手资料，访问日期均为 2026-08-01。

## 结论先行：首选 GitLab + Dagger

### 单页标题

**从工作项到可复用执行单元：GitLab 编排 Agent 工作，Dagger 把 Agent 环境写成代码**

### 页面唯一主张

**企业 Agentic CI/CD 的新增能力，不只是让 Agent “会修代码”，而是把工作拆成两类可复用单元：GitLab 把 Issue、MR、Pipeline Event 和多 Agent Flow 变成可触发的交付作业；Dagger 则把 Agent 可使用的目录、容器、函数和 MCP 工具定义为可组合的执行环境。前者组织工作，后者产品化执行。**

这组最适合新增一页，因为它不是重复 GitHub 的“声明—编译—Safe Output”，也不是重复 Harness 的“平台 Knowledge Graph + Pipeline Context”。它切入的是两个更可移植的能力：**事件化的任务编排**与**环境即代码的 Agent Runtime**。

### 两家公司各自可借鉴的能力

| 公司 | 可借鉴能力 | 一手事实与状态 |
|---|---|---|
| GitLab | 1. 将 Issue、MR、评论提及、Pipeline Event 等转成 Flow 触发器。<br>2. 用 Custom Flows 定义多步骤、多 Agent 的可复用交付作业。<br>3. Flow 直接在 GitLab CI/CD 中执行，能将 Agent 工作放回已存在的项目上下文。<br>4. 用 AI Catalog 作为 Flow 的创建、管理与分发入口。 | Custom Flows 于 **2026-07-16**（GitLab 19.2）**GA**；官方列出 YAML workflow、Multi-agent orchestration、原生触发器和 CI/CD 执行。[GitLab 19.2 Release Notes](https://docs.gitlab.com/releases/19/gitlab-19-2-released/)；[Flows Docs](https://docs.gitlab.com/user/duo_agent_platform/flows/)。平台整体于 **2026-01-15 GA**；具体 CLI、Orbit 等周边能力须逐项标注。[GitLab Platform GA](https://about.gitlab.com/press/releases/2026-01-15-gitlab-announces-duo-agent-platform-general-availability/) |
| Dagger | 1. 用 `Environment` 将 Directory、Container、Module、变量组合为 Agent 的任务环境。<br>2. 让 LLM 自动发现/调用该环境中的 Dagger Functions，而不是在 prompt 中硬编码工具步骤。<br>3. 把自定义模块暴露为 MCP server，或把外部 MCP server 接进 LLM Environment。<br>4. 以任意主流语言定义、复用和串联 Agent workflow，连同容器环境一起运行。 | Dagger 官方文档将其定位为 Agent runtime/programming environment；`LLM`、Environment、Function Tool Use 和 MCP 双向集成均有现行文档与 Quickstart。[Dagger LLM Integration](https://docs.dagger.io/features/llm/)；[Build an AI Agent](https://docs.dagger.io/getting-started/quickstarts/agent/)。资料未标整体 GA/Preview，不自行补充产品状态。 |

### 共同架构与版式

```text
GitLab：交付事件 / 工作项
Issue · MR · Pipeline Event
           ↓ 触发 / 拆分
Custom Flow：多个专长 Agent
           ↓ 派发任务
Dagger Environment：目录 · 容器 · Function · MCP
           ↓ 工具调用 / 测试 / 产物
可审查 MR、Pipeline 结果、Artifact
```

建议采用“**上游工作编排 → 下游可编程执行**”的左右双栏：

- 左栏 GitLab 用一张“事件 → Flow → 多 Agent → CI Job”的任务网络，强调作业可被触发、拆分、复用；不要展开 Composite Identity、HITL 或审计细节。
- 右栏 Dagger 用一张“Environment → Function Tools / Container / MCP → Agent”的环境装配图，强调工具和运行环境是代码，而非一次性 prompt。
- 中轴只写一句：**“任务上下文决定做什么；环境契约决定能怎样做。”**
- 页脚边界一句即可：实际写入、合并、部署仍经各自 CI/CD 的权限、测试与审批，不由 Flow 或 MCP 可调用性自动授予。

### 对现有五页的增量

| 已有页 | 本页刻意不重复的部分 | 新增部分 |
|---|---|---|
| GitHub Agentic Workflows | 不再讲 Markdown Compiler、lock.yml、Safe Output。 | 从“工作流声明的编译”转为“工作项驱动的 Flow 编排 + 程序化 Agent Environment”。 |
| Harness CI | 不再讲 Knowledge Graph、HQL、Pipeline Context 或 Agent 权限层。 | 用通用的 Environment/Function 模型说明怎样沉淀跨语言、可组合的 Agent 工作单元。 |
| CI 自愈 | 不讲失败分类、修复 Diff 或有界写回。 | 关注修复任务怎样从事件进入 Flow、怎样装配可复现工具环境。 |
| 基础设施 | 不讲 Runner/缓存/Sidecar/容量。 | 关注 Agent Runtime 的**编程抽象**，而不是算力与执行基础设施。 |
| CLI / MCP | 不讨论 CLI vs MCP 的选型。 | MCP 仅作为 Dagger Environment 的可插拔工具来源，重点是“环境装配”。 |

### 必须保持的事实边界

- 不将 GitLab Platform 的 GA 延伸到所有 Duo/Orbit/CLI 功能；本页只主张 Custom Flows 已 GA 的明确能力。
- Dagger 文档没有给出统一 GA/Preview 标签；只陈述当前文档可证明的 API/运行模型。
- “环境即代码更可复现”是架构意图，不等于每个容器/外部 MCP 调用天然可重放；不要用它替代 Test、Scan 或 Policy。

## 候选排序与取舍

| 排名 | 公司组合 | 共同单页主张 | 新增能力与机制独特性 | 与现有五页的非重复性 | 产品状态与主要限制 |
|---|---|---|---|---|---|
| 1 | **GitLab + Dagger** | 交付作业需同时具备事件化任务编排与代码化 Agent Environment。 | Flow/事件驱动多 Agent + Environment/Function/Container/MCP 装配。 | 高：GitLab 与 GitHub/Harness 有相邻处，但 Dagger 的程序化环境抽象带来新维度。 | GitLab Custom Flows GA；Dagger按文档能力，未见统一状态标签。 |
| 2 | **Dagger + Buildkite** | Agentic CI 应把 Agent 设计成可组合 workload：环境/工具以代码定义，执行以 Pipeline 原语承载。 | Dagger 产出可编程 runtime；Buildkite 把 Agent 作为 pipeline 的 builder、tool user 与 step。 | 中高：与基础设施、CLI/MCP 邻近，但可严格聚焦“可组合 workload”。 | Buildkite 文档能力未给整体状态；Model Provider 当前只列 Anthropic。 |
| 3 | **GitLab + CircleCI** | Agentic delivery 的吞吐来自“事件化任务队列 + Agent 工作中的快速验证”。 | GitLab 负责将组织工作持续投入 Flow，CircleCI Sidecar/Microbuild 负责快速返回验证反馈。 | 中：会碰到 GitHub/Harness 编排和 Nx/CircleCI 自愈的既有材料。 | GitLab Custom Flows GA；CircleCI Sidecar 面向全计划可用，但性能数据属厂商测量。 |
| 4 | **CircleCI + Buildkite** | Agent-first CI 同时需要低延迟验证内环与可组合 Pipeline 执行面。 | CircleCI 的 Snapshot/Sidecar/Microbuild；Buildkite 的 MCP/Model Provider/Agent Step。 | 中低：现有 CI 自愈和基础设施页已覆盖这两个机制。 | CircleCI 功能状态按部件区分；Buildkite整体未标状态。 |
| 5 | **GitLab + JFrog** | 交付平台的 Agent 作业可以连接供应链能力资产。 | Flow/Agent 与 MCP/Agent Package Registry 的互补。 | 低：容易滑回安全、可信、MCP 治理，偏离本页“能力”主体。 | GitLab Flow GA；JFrog MCP Registry GA，Agent Skills Registry Beta。 |

## 2. Dagger + Buildkite：可组合的 Agentic CI workload

### 单页标题与主张

**标题：把 Agent 做成 CI 的可组合 workload：代码定义环境，Pipeline 承载执行**

**主张：Dagger 把 Agent 的工具、容器和验证函数封装为 Environment；Buildkite 同时让 Agent 读写 Pipeline 的实时上下文并作为 Step 执行。企业可以将 Agent 从零散脚本升级为可复用的 CI 工作单元。**

### 两家公司各自 3—4 项可借鉴能力

| Dagger | Buildkite |
|---|---|
| Environment 组合目录、容器、模块与变量。 | Skills 帮助 coding agent 生成和维护 Pipeline。 |
| Function 自动成为 LLM 可发现的工具。 | MCP 读取 Build State/Log、触发 Build、迭代 Pipeline。 |
| `LLM.withMCPServer` 接入外部服务；模块能外露为 MCP。 | Model provider 将 LLM 放进 Pipeline Step，并传入日志、artifact、policy、实时 pipeline data。 |
| 多语言、模块化 workflow 与容器环境一体定义。 | 同一平台既让 Agent 使用 CI，又让 CI 承载 Agent。 |

事实与状态：Dagger 依据 [LLM Integration Docs](https://docs.dagger.io/features/llm/)；Buildkite 依据 [AI Agents in Pipelines](https://buildkite.com/docs/platform/ai-agents) 与 [Model Providers](https://buildkite.com/docs/apis/model-providers)。Buildkite 当前 Model Provider 文档只列 Anthropic，并由组织管理员连接；没有整体 GA/Preview 标签。

版式：左“Dagger：Environment as Code”，右“Buildkite：Pipeline as Runtime”，中间用一个可复用 Agent module 的箭头连起来。页脚提示 Buildkite 的 tool 调用/Step 行为仍继承 job token 与平台权限。

为什么不排第一：它最容易被误读成 CLI/MCP 技术选型页，且与基础设施页的“Agent Runtime/Tool 面”已有较多重叠；需要非常严格地把受众锁定为平台工程团队。

## 3. GitLab + CircleCI：任务队列与验证内环

### 单页标题与主张

**标题：让 Agent 持续交付：工作项不断进入 Flow，验证反馈持续回到 Agent**

**主张：GitLab 用仓库事件和 Custom Flow 组织多 Agent 的异步交付任务；CircleCI 用 Sidecar/Microbuild 把增量验证前移到 Agent 工作中。规模化 Agentic CI 的能力瓶颈从“生成代码”转向任务吞吐和反馈速度。**

### 两家公司各自 3—4 项可借鉴能力

| GitLab | CircleCI |
|---|---|
| Issue/MR/mention/Pipeline Event 触发 Flow。 | 预热 sidecar 运行预配置验证环境。 |
| YAML 定义可复用、多 Agent Custom Flow。 | Microbuild 以增量 patch 进行小粒度验证。 |
| Flow 在 GitLab CI/CD 内运行，承接项目上下文。 | 将精简、可行动的验证反馈返回给 Agent。 |
| AI Catalog 创建、启用和管理 Flow。 | 完整 CI 仍保留作系统级外环验证。 |

事实与状态：GitLab 见 [19.2 Release Notes](https://docs.gitlab.com/releases/19/gitlab-19-2-released/)；CircleCI Sidecar 说明其面向所有计划可用（页面最后更新 **2026-05-22**），工程文章说明 snapshot/microbuild 与 Agent feedback，但效率数据只来自厂商自身。[CircleCI Sidecars](https://circleci.com/blog/chunk-sidecars/)；[Agentic Validation Engineering Post](https://circleci.com/blog/agentic-validation-needs-different-infrastructure/)

版式：上层画 GitLab“事件漏斗→Flow”；下层画 CircleCI“Agent 修改→Sidecar/Microbuild→反馈”；将两者汇成“下一轮 Agent 工作”。

为什么不排第一：现有 Nx 自愈页已经引用 CircleCI 的内循环，并比较 GitLab/Harness 的相关机制。若没有把主线彻底改为“任务吞吐与反馈架构”，会造成明显重复。

## 4. CircleCI + Buildkite：两种 CI 原生 Agent 能力

### 单页标题与主张

**标题：CI 不再只跑检查：一条为 Agent 反馈加速，一条让 Agent 成为 Pipeline Step**

**主张：CircleCI 通过预热 Sidecar/Microbuild 缩短 Agent 每轮验证；Buildkite 通过 MCP、Model Provider 与 Step 把 Agent 接入 Pipeline。两者说明 CI 平台正在同时演化为 Agent 的反馈层与执行层。**

### 两家公司各自 3—4 项可借鉴能力

| CircleCI | Buildkite |
|---|---|
| Chunk 分析失败、Flaky test 与 pipeline 配置问题。 | Agent 可用 Skills 构建和维护 Pipeline。 |
| Sidecar 提供预热、配置好的验证环境。 | MCP 获取 Log/State 并触发 Build。 |
| Microbuild 仅传入增量 patch，减少环境准备和日志开销。 | Model provider 在 Pipeline Step 中运行模型。 |
| 内循环与完整 CI 外环分工。 | Agentic plugin/step 可处理失败分析等任务。 |

事实与状态：CircleCI [Chunk 发布](https://circleci.com/blog/introducing-chunk/)（发布 **2025-09-23**）与 [Sidecars](https://circleci.com/blog/chunk-sidecars/)；Buildkite [AI Agents Docs](https://buildkite.com/docs/platform/ai-agents)。CircleCI 的部分密钥/自动化能力有独立 Beta/Preview 标签，不能把整个产品统一称为 GA；Buildkite 没有整体状态标签。

版式：两条平行的 Agent 工作回路，CircleCI 突出“快反馈”，Buildkite 突出“原生 Step”。

为什么不排第一：与已有 CI 自愈页的“失败—修复—复验”及基础设施页的“内环/外环、MCP/Agent Step”重合最大；适合做深挖备选，不适合作为第六页首选。

## 未选 GitLab + JFrog 的原因

GitLab 的 Custom Flow 与 JFrog MCP/Agent Package 能形成“作业 + 能力资产”的对照，但最自然的叙事仍是资产准入、工具权限、供应链与治理。它会把页面重心拉回安全/可信/MCP 管理，偏离本次要求的“新增能力、机制与企业借鉴”，故不作为首选。

## 进入正式页面前的 Deep Dive 门槛

1. 若采用首选，建立一个跨公司专题，验证“GitLab Flow 如何调用/承接 Dagger workload”只是架构借鉴而非官方集成声明；图中不得画成已存在的产品连接器。
2. 分别审计 GitLab Custom Flow 的套餐、版本、触发器与执行限制，以及 Dagger Function/Environment/MCP 的版本、运行环境和可复现边界。
3. 页面每个“能力”只保留一个可回链的一手证据；把身份、安全与权限约束压缩至页脚，不能抢占标题或主体。
4. 只有专题 `presentation_ready: true` 后，才能将选题迁入 `80_presentations/`。
